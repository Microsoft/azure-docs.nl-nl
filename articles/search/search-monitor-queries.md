---
title: Query's bewaken
titleSuffix: Azure Cognitive Search
description: Bewaak de metrische gegevens van query's voor prestaties en door voer. Verzamel en analyseer invoer van query reeksen in resource Logboeken.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 169a90c12b30e0d083ce5c53ab7c6dd2495c4c23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592383"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Query aanvragen bewaken in azure Cognitive Search

In dit artikel wordt uitgelegd hoe u de prestaties en het volume van query's meet met metrische gegevens en bron logboek registratie. Ook wordt uitgelegd hoe u de invoer termen kunt verzamelen die worden gebruikt in query's-benodigde informatie wanneer u het hulp programma en de effectiviteit van uw zoek verzameling moet beoordelen.

Historische gegevens die feeds in metrieken, worden 30 dagen bewaard. Voor een langere retentie of voor het rapporteren van operationele gegevens en query reeksen moet u een [Diagnostische instelling](search-monitor-logs.md) inschakelen waarmee een opslag optie wordt opgegeven voor het persistent maken van vastgelegde logboeken en metrieken.

Voor waarden die de integriteit van gegevens meting maximaliseren zijn:

+ Gebruik een factureer bare service (een service die is gemaakt op basis van de Basic-of Standard-laag). De gratis service wordt gedeeld door meerdere abonnees, waarmee een bepaalde hoeveelheid volatiliteit wordt geïntroduceerd naarmate de werk belasting wordt geschoven.

+ Gebruik één replica en partitie, indien mogelijk, om een opgenomen en geïsoleerde omgeving te maken. Als u meerdere replica's gebruikt, worden de metrische gegevens van de query gemiddeld verdeeld over meerdere knoop punten, wat de nauw keurigheid van de resultaten kan verlagen. Op dezelfde manier betekenen meerdere partities dat gegevens worden gedeeld, met het potentieel dat sommige partities andere gegevens kunnen hebben als de indexering ook wordt uitgevoerd. Bij het afstemmen van query prestaties biedt één knoop punt en partitie een stabielere omgeving voor het testen.

> [!Tip]
> Met aanvullende client-side code en Application Insights, kunt u ook doorgestuurde gegevens vastleggen voor een dieper inzicht in wat het belang van uw toepassings gebruikers trekt. Zie voor meer informatie [Search Traffic Analytics](search-traffic-analytics.md)(Engelstalig).

## <a name="query-volume-qps"></a>Query volume (QPS)

Volume wordt gemeten als **Zoek Query's per seconde** (qps), een ingebouwde metriek die kan worden gerapporteerd als gemiddeld, aantal, minimum of maximum waarden van query's die in een venster van één minuut worden uitgevoerd. Een interval van één minuut (TimeGrain = "PT1M") voor metrische gegevens is in het systeem opgelost.

Het is gebruikelijk om query's uit te voeren in milliseconden, zodat alleen query's die als seconden worden gemeten, worden weer gegeven in metrische gegevens.

| Aggregatietype | Description |
|------------------|-------------|
| Gemiddeld | Het gemiddelde aantal seconden binnen een minuut gedurende welke de uitvoering van de query heeft plaatsgevonden.|
| Count | Het aantal metrische gegevens dat is verzonden naar het logboek binnen het interval van één minuut. |
| Maximum | Het hoogste aantal Zoek query's per seconde dat is geregistreerd tijdens een minuut. |
| Minimum | Het laagste aantal Zoek query's per seconde dat is geregistreerd tijdens een minuut.  |
| Sum | De som van alle query's die in de minuut worden uitgevoerd.  |

U kunt bijvoorbeeld binnen één minuut een patroon hebben dat er ongeveer als volgt uitziet: één seconde van een hoge belasting die het maximum voor SearchQueriesPerSecond is, gevolgd door 58 seconden gemiddelde belasting, en ten slotte één seconde met slechts één query, wat het minimum is.

Een ander voor beeld: als een knoop punt 100 metrische gegevens verzendt, waarbij de waarde van elke metriek 40 is, is ' count ' 100, ' sum ' is 4000, ' Average ' is 40 en ' Max ' is 40.

## <a name="query-performance"></a>Queryprestaties

Voor de gehele service worden de query prestaties gemeten als Zoek latentie (hoelang een query moet worden voltooid) en vertraagde query's die zijn verwijderd als gevolg van bron conflicten.

### <a name="search-latency"></a>Zoek latentie

| Aggregatietype | Latentie | 
|------------------|---------|
| Gemiddeld | Gemiddelde query duur in milliseconden. | 
| Count | Het aantal metrische gegevens dat is verzonden naar het logboek binnen het interval van één minuut. |
| Maximum | De langste uitvoering van een query in het voor beeld. | 
| Minimum | De kortste uitvoering van een query in het voor beeld.  | 
| Totaal | Totale uitvoerings tijd van alle query's in het voor beeld, uitgevoerd binnen het interval (een minuut).  |

Bekijk het volgende voor beeld van metrische gegevens over **Zoek latentie** : 86-query's werden voor bereid met een gemiddelde duur van 23,26 milliseconden. Een minimum van 0 geeft aan dat sommige query's zijn verwijderd. De langste uitvoering van de query duurde 1000 milliseconden te volt ooien. Totale uitvoerings tijd is 2 seconden.

![Latentie aggregaties](./media/search-monitor-usage/metrics-latency.png "Latentie aggregaties")

### <a name="throttled-queries"></a>Vertraagde query's

Vertraagde query's verwijzen naar query's die in plaats van proces worden verwijderd. In de meeste gevallen is beperking een normaal onderdeel van het uitvoeren van de service.  Het is niet noodzakelijkerwijs een indicatie dat er iets mis is.

Beperking wordt weer gegeven wanneer het aantal aanvragen dat momenteel wordt verwerkt, de beschik bare resources overschrijdt. U ziet mogelijk een verhoging van de vertraagde aanvragen wanneer een replica uit de rotatie of tijdens het indexeren wordt gehaald. Aanvragen voor query's en indexering worden verwerkt door dezelfde set resources.

De service bepaalt of aanvragen worden verwijderd op basis van Resource verbruik. Het percentage resources verbruikt over het geheugen, de CPU en de schijf-i/o worden gemiddeld gedurende een bepaalde periode geconsumeerd. Als dit percentage een drempel waarde overschrijdt, worden alle aanvragen voor de index beperkt tot het aantal aanvragen wordt verminderd. 

Afhankelijk van uw client kan een beperkt verzoek op de volgende manieren worden aangegeven:

+ Een service retourneert een fout bericht dat u te veel aanvragen verzendt. Probeer het later opnieuw. 
+ Een service retourneert een fout code van 503 die aangeeft dat de service momenteel niet beschikbaar is. 
+ Als u de portal gebruikt (bijvoorbeeld Search Explorer), wordt de query op de achtergrond verwijderd en moet u opnieuw op zoeken klikken.

Gebruik de metrische gegevens over vertraagde **Zoek query's** om beperkte query's te bevestigen. U kunt metrische gegevens in de portal verkennen of een metrische gegevens van een waarschuwing maken, zoals beschreven in dit artikel. Voor query's die in het steekproef interval zijn verwijderd, gebruikt u *totaal* om het percentage query's te verkrijgen dat niet is uitgevoerd.

| Aggregatietype | Beperking |
|------------------|-----------|
| Gemiddeld | Het percentage query's dat binnen het interval is verwijderd. |
| Count | Het aantal metrische gegevens dat is verzonden naar het logboek binnen het interval van één minuut. |
| Maximum | Het percentage query's dat binnen het interval is verwijderd.|
| Minimum | Het percentage query's dat binnen het interval is verwijderd. |
| Totaal | Het percentage query's dat binnen het interval is verwijderd. |

Voor het **beperkende Zoek Query's percentage**, minimum, maximum, gemiddelde en totaal heeft dezelfde waarde: het percentage Zoek query's dat is beperkt tot het totale aantal Zoek query's gedurende één minuut.

In de volgende scherm afbeelding is het eerste getal het aantal (of het aantal metrische gegevens dat naar het logboek wordt verzonden). Extra aggregaties, die bovenaan worden weer gegeven of bij het aanwijzen van de metriek, zijn gemiddelde, maximum en totaal. In dit voor beeld zijn er geen aanvragen verloren gegaan.

![Beperkte aggregaties](./media/search-monitor-usage/metrics-throttle.png "Beperkte aggregaties")

## <a name="explore-metrics-in-the-portal"></a>Metrische gegevens in de portal verkennen

Voor een beknopt overzicht van de huidige nummers ziet u op het tabblad **bewaking** op de pagina overzicht van de service drie metrische gegevens (**Zoek latentie**, **Zoek query's per seconde (per Zoek eenheid)**, **beperkt aantal Zoek query's in procenten**) over vaste intervallen gemeten in uren, dagen en weken, met de optie om het aggregatie type te wijzigen.

Open Metrics Explorer in het menu **bewaking** , zodat u de gegevens kunt inzoomen en visualiseren om trends of afwijkingen te verkennen. Meer informatie over metrische gegevens Verkenner vindt u in deze [zelf studie over het maken van een metrieke grafiek](../azure-monitor/essentials/tutorial-metrics-explorer.md).

1. Selecteer in de sectie bewaking **metrische gegevens** om de metrics Explorer te openen met de scope die is ingesteld op uw zoek service.

1. Onder metrische waarde, kiest u een in de vervolg keuzelijst en bekijkt u de lijst met beschik bare aggregaties voor een voorkeurs type. De aggregatie definieert hoe de verzamelde waarden voor elk tijds interval worden gesampled.

   ![Metrics Explorer voor QPS metric](./media/search-monitor-usage/metrics-explorer-qps.png "Metrics Explorer voor QPS metric")

1. Stel in de rechter bovenhoek het tijds interval in.

1. Kies een visualisatie. De standaard waarde is een lijn diagram.

1. Laag extra aggregaties door **metrische gegevens toevoegen** te kiezen en verschillende aggregaties te selecteren.

1. Zoom in op een interesse gebied in het lijn diagram. Plaats de muisaanwijzer aan het begin van het gebied, klik erop en houd de linkermuis ingedrukt, sleep naar de andere kant van het gebied en laat de knop los. In de grafiek wordt ingezoomd op dat tijdsbereik.

## <a name="identify-strings-used-in-queries"></a>Teken reeksen identificeren die worden gebruikt in query's

Wanneer u bron logboek registratie inschakelt, worden query aanvragen in de tabel **AzureDiagnostics** vastgelegd. Als vereiste moet u [resource registratie](search-monitor-logs.md)al hebben ingeschakeld, een log Analytics-werk ruimte of een andere opslag optie opgeven.

1. Selecteer in de sectie bewaking **Logboeken** om een leeg query venster te openen in log Analytics.

1. Voer de volgende expressie uit om Query's te zoeken. Zoek opdrachten, een tabellaire resultaatset retour neren die bestaat uit de bewerkings naam, de query reeks, de opgevraagde index en het aantal documenten dat is gevonden. De laatste twee instructies bevatten query reeksen die bestaan uit een lege of niet-opgegeven zoek opdracht, via een voor beeld-index, waardoor de ruis in uw resultaten wordt uitgesplitst.

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2020-06-30&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. U kunt eventueel een kolom filter op *Query_s* instellen om te zoeken naar een specifieke syntaxis of teken reeks. U kunt bijvoorbeeld filteren op *is gelijk aan* `?api-version=2020-06-30&search=*&%24filter=HotelName` ).

   ![Teken reeksen van vastgelegde query's](./media/search-monitor-usage/log-query-strings.png "Teken reeksen van vastgelegde query's")

Hoewel deze methode werkt voor ad-hoc onderzoek, kunt u met het maken van een rapport de query reeksen samen voegen en presen teren in een lay-out die bevorderlijk is voor analyse.

## <a name="identify-long-running-queries"></a>Langlopende query's identificeren

Voeg de kolom duur toe om de getallen voor alle query's op te halen, niet alleen de waarden die worden opgehaald als metriek. Als u deze gegevens sorteert, ziet u welke query's het langst duren om te volt ooien.

1. Selecteer in de sectie bewaking **Logboeken** om te zoeken naar logboek gegevens.

1. Voer de volgende query uit om query's te retour neren, gesorteerd op duur in milliseconden. De query's die het langst lopen, zijn bovenaan.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![Query's sorteren op duur](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "Query's sorteren op duur")

## <a name="create-a-metric-alert"></a>Een waarschuwing voor metrische gegevens maken

Een metrische waarschuwing brengt een drempel waarde aan waarbij u een melding ontvangt of een corrigerende actie triggert die u vooraf definieert. 

Voor een zoek service is het gebruikelijk om een metrische waarschuwing te maken voor zoek latentie en vertraagde query's. Als u weet wanneer query's worden verwijderd, kunt u zoeken naar oplossingen die de belasting verminderen of de capaciteit verg Roten. Als er bijvoorbeeld vertraagde query's toenemen tijdens het indexeren, kunt u deze uitstellen tot de query activiteit subkanten heeft.

Bij het pushen van de limieten van een bepaalde replica partitie, is het instellen van waarschuwingen voor query volume drempels (QPS) ook nuttig.

1. Selecteer in de sectie controle de optie **waarschuwingen** en klik vervolgens op **+ nieuwe waarschuwings regel**. Zorg ervoor dat uw zoek service is geselecteerd als de resource.

1. Klik onder voor waarde op **toevoegen**.

1. Signaal logica configureren. Voor signaal type kiest u **metrische gegevens** en selecteert u vervolgens het signaal.

1. Nadat u het signaal hebt geselecteerd, kunt u een grafiek gebruiken om historische gegevens te visualiseren voor een weloverwogen beslissing over hoe u kunt door gaan met het instellen van voor waarden.

1. Schuif vervolgens omlaag naar waarschuwings logica. Voor het testen van het concept kunt u een kunst matige lage waarde voor test doeleinden opgeven.

   ![Waarschuwingslogica](./media/search-monitor-usage/alert-logic-qps.png "Waarschuwingslogica")

1. Vervolgens geeft u een actie groep op of maakt u deze. Dit is het antwoord dat moet worden aangeroepen wanneer aan de drempel wordt voldaan. Dit kan een push melding of een geautomatiseerd antwoord zijn.

1. Geef als laatste de waarschuwings Details op. Geef een naam en beschrijving van de waarschuwing, wijs een Ernst waarde toe en specificeer of u de regel in de status ingeschakeld of uitgeschakeld wilt maken.

   ![Waarschuwings Details](./media/search-monitor-usage/alert-details.png "Meldingsdetails")

Als u een e-mail melding hebt opgegeven, ontvangt u een e-mail bericht van ' Microsoft Azure ' met een onderwerpregel van ' Azure: geactiveerde Ernst: ' 3 `<your rule name>` '.

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, raadpleegt u de grond beginselen van de controle van de zoek service voor meer informatie over het volledige aanbod van de mogelijkheden voor toezicht.

> [!div class="nextstepaction"]
> [Bewerkingen en activiteiten bewaken in azure Cognitive Search](search-monitor-usage.md)