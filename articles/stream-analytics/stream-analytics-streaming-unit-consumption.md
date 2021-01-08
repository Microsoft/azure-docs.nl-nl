---
title: Streaming-eenheden in Azure Stream Analytics
description: In dit artikel worden de instellingen voor streaming-eenheden en andere factoren beschreven die van invloed zijn op de prestaties in Azure Stream Analytics.
author: JSeb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: a5a0e6feba966d2d10c5cd36432c3d5db172a795
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98020006"
---
# <a name="understand-and-adjust-streaming-units"></a>Streaming-eenheden begrijpen en aanpassen

Streaming-eenheden (SUs) vertegenwoordigt de computer resources die zijn toegewezen om een Stream Analytics taak uit te voeren. Hoe hoger het aantal streaming-eenheden, hoe meer CPU- en geheugenresources worden toegewezen voor uw taak. Met deze capaciteit kunt u zich richten op de query logica en abstracten de behoefte aan het beheer van de hardware om uw Stream Analytics-taak tijdig uit te voeren.

Om verwerking met lage latentie te bereiken, voeren Azure Stream Analytics-taken alle verwerking in het geheugen uit. Als er onvoldoende geheugen beschikbaar is, mislukt de streaming-taak. Als gevolg hiervan is het belang rijk om voor een productie taak het resource gebruik van een streaming-taak te controleren en ervoor te zorgen dat er voldoende resources zijn toegewezen om te zorgen dat de taken met 24/7 worden uitgevoerd.

De metrische gegevens van het gebruik SU%, die tussen 0% en 100% liggen, beschrijft het geheugen verbruik van uw workload. Voor een streaming-taak met minimale footprint is deze metriek meestal tussen 10% en 20%. Als SU% gebruik hoog is (boven 80%), of als de invoer gebeurtenissen een achterstand ontvangen (zelfs met een laag verbruik van minder dan het CPU-gebruik), zijn voor uw werk belasting waarschijnlijk meer reken bronnen vereist, waarmee u het aantal SUs kunt verhogen. Het is het beste om de SU-metriek onder 80% te hand haven voor incidentele pieken. Als u wilt reageren op verhoogde werk belastingen en streaming-eenheden wilt verhogen, kunt u een waarschuwing instellen van 80% op de metrische gegevens van het SU-gebruik. U kunt ook het watermerk vertraging en de metrische gegevens voor de achterstand gebruiken om te zien of er sprake is van een impact.

## <a name="configure-stream-analytics-streaming-units-sus"></a>Stream Analytics streaming-eenheden configureren (SUs)
1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Zoek in de lijst met resources de Stream Analytics-taak die u wilt schalen en open deze. 

3. Selecteer op de pagina taak onder de kop **configureren** de optie **schalen**. Het standaard aantal SUs is 3 bij het maken van een taak.

    ![Configuratie van Stream Analytics taak Azure Portal][img.stream.analytics.preview.portal.settings.scale]
    
4. Gebruik de schuif regelaar om de SUs voor de taak in te stellen. U ziet dat u beperkt bent tot specifieke SU-instellingen. 
5. U kunt het aantal SUs dat is toegewezen aan uw taak wijzigen, zelfs wanneer dit wordt uitgevoerd. Dit is niet mogelijk als uw taak gebruikmaakt van een [niet-gepartitioneerde uitvoer](./stream-analytics-parallelization.md#query-using-non-partitioned-output) of [een query met meerdere stappen heeft met andere partities op basis van waarden](./stream-analytics-parallelization.md#multi-step-query-with-different-partition-by-values). U kunt de keuze van een aantal SU-waarden beperken wanneer de taak wordt uitgevoerd. 

## <a name="monitor-job-performance"></a>Taak prestaties bewaken
Met behulp van de Azure Portal kunt u de door Voer van een taak volgen:

![Taken Azure Stream Analytics bewaken][img.stream.analytics.monitor.job]

Bereken de verwachte door Voer van de werk belasting. Als de door Voer kleiner is dan verwacht, het afstemmen van de invoer partitie, het afstemmen van de query en het toevoegen van SUs aan uw taak.

## <a name="how-many-sus-are-required-for-a-job"></a>Hoeveel streaming-eenheden zijn vereist voor een taak?

Het kiezen van het aantal vereiste SUs voor een bepaalde taak is afhankelijk van de partitie configuratie voor de invoer en de query die in de taak is gedefinieerd. Op de **schaal** pagina kunt u het juiste aantal SUs instellen. Het is een best practice om meer SUs toe te wijzen dan nodig is. De Stream Analytics verwerkings engine optimaliseert voor latentie en door voer tegen de kosten van het toewijzen van extra geheugen.

Over het algemeen is het best practice te beginnen met 6 SUs voor query's waarvoor geen **partitie** wordt gebruikt. Bepaal vervolgens het zoete punt met behulp van een proef versie en fout methode waarbij u het aantal SUs wijzigt nadat u de representatieve hoeveelheid gegevens hebt door gegeven en de metrische kosten voor het gebruik van% kunt controleren. Het maximum aantal streaming-eenheden dat door een Stream Analytics taak kan worden gebruikt, is afhankelijk van het aantal stappen in de query die is gedefinieerd voor de taak en het aantal partities in elke stap. Meer informatie over de limieten vindt u [hier](./stream-analytics-parallelization.md#calculate-the-maximum-streaming-units-of-a-job).

Ga voor meer informatie over het kiezen van het juiste aantal SUs naar deze pagina: [schaal Azure stream Analytics taken om de door voer te verhogen](stream-analytics-scale-jobs.md)

> [!Note]
> U kunt kiezen hoeveel SUs vereist is voor een bepaalde taak, afhankelijk van de partitie configuratie voor de invoer en voor de query die is gedefinieerd voor de taak. U kunt voor een taak Maxi maal uw quotum in SUs selecteren. Elk Azure-abonnement heeft standaard een quotum van Maxi maal 500 SUs voor alle analyse taken in een bepaalde regio. Neem contact op met [Microsoft ondersteuning](https://support.microsoft.com)om SUs te verhogen voor uw abonnementen buiten dit quotum. Geldige waarden voor SUs per taak zijn 1, 3, 6 en Maxi maal 6.

## <a name="factors-that-increase-su-utilization"></a>Factoren die het gebruikspercentage van streaming-eenheden verhogen 

Tijdelijke (tijd georiënteerde) query-elementen zijn de belangrijkste set stateful Opera tors van Stream Analytics. Stream Analytics beheert de status van deze bewerkingen intern namens de gebruiker door het geheugen gebruik te beheren, de controle punten voor tolerantie te beheersen en status herstel tijdens service-upgrades. Hoewel Stream Analytics de statussen volledig beheert, zijn er een aantal best practice aanbevelingen waarmee gebruikers rekening moeten houden.

Houd er rekening mee dat een taak met complexe query logica mogelijk een hoog SU%-gebruik kan hebben, zelfs wanneer er niet continu invoer gebeurtenissen worden ontvangen. Dit kan gebeuren na een onverwachte piek in de invoer-en uitvoer gebeurtenissen. De taak kan de status in het geheugen blijven behouden als de query complex is.

Het gebruik van% voor een korte periode kan plotseling tot 0 worden terugvallen voordat u terugkeert naar de verwachte niveaus. Dit gebeurt als gevolg van tijdelijke fouten of door het systeem geïnitieerde upgrades. Het verhogen van het aantal streaming-eenheden voor een taak vermindert mogelijk het gebruik van SU% als uw query niet [volledig parallel](./stream-analytics-parallelization.md)is.

Gebruik bij het vergelijken van het gebruik voor een bepaalde tijd [metrische gegevens over de gebeurtenis frequentie](stream-analytics-monitoring.md). Met metrische gegevens voor InputEvents en OutputEvents wordt weer gegeven hoeveel gebeurtenissen zijn gelezen en verwerkt. Er zijn metrische gegevens die ook het aantal fout gebeurtenissen aangeven, zoals fouten bij het deserialiseren. Wanneer het aantal gebeurtenissen per tijds eenheid toeneemt, neemt SU% in de meeste gevallen toe.

## <a name="stateful-query-logic-in-temporal-elements"></a>Stateful query logica in tijdelijke elementen
Een van de unieke mogelijkheden van Azure Stream Analytics taak is het uitvoeren van stateful verwerking, zoals venster aggregaties, tijdelijke samen voegingen en tijdelijke analytische functies. Elk van deze opera tors houdt status informatie. De maximale venster grootte voor deze query-elementen is zeven dagen. 

Het tijdelijke venster-concept wordt in verschillende Stream Analytics query-elementen weer gegeven:
1. Statistische functies in Vensters: groeperen op Tumblingvenstertriggers, verspringen en schuif Vensters

2. Tijdelijke samen voegingen: samen voegen met de functie DATEDIFF

3. Tijdelijke analyse functies: ISFIRST, LAST en LAG met de limiet duur

De volgende factoren beïnvloeden het geheugen dat wordt gebruikt (onderdeel van metrische gegevens van streaming-eenheden) door Stream Analytics taken:

## <a name="windowed-aggregates"></a>Statistische functies in Vensters
Het verbruikte geheugen (status grootte) voor een statistische functie voor een venster is niet altijd direct evenredig met de grootte van het venster. In plaats daarvan is het geheugen dat wordt verbruikt evenredig met de kardinaliteit van de gegevens of het aantal groepen in elk tijd venster.


In de volgende query is bijvoorbeeld het nummer dat is gekoppeld aan de `clusterid` kardinaliteit van de query. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Om problemen te verhelpen die worden veroorzaakt door een hoge kardinaliteit in de vorige query, kunt u gebeurtenissen verzenden naar Event hub gepartitioneerd door `clusterid` en de query uitschalen door het systeem toe te staan elke invoer partitie afzonderlijk te verwerken met behulp van de **partitie** , zoals wordt weer gegeven in het onderstaande voor beeld:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

Wanneer de query is gepartitioneerd, wordt deze verspreid over verschillende knooppunten. Als gevolg hiervan wordt het aantal `clusterid` waarden dat op elk knoop punt binnenkomt, kleiner, waardoor de kardinaliteit van de Group by-operator wordt verminderd. 

Event hub-partities moeten worden gepartitioneerd door de groeperings sleutel om te voor komen dat een stap hoeft te worden verkleind. Zie [Event hubs Overview](../event-hubs/event-hubs-about.md)voor meer informatie. 

## <a name="temporal-joins"></a>Tijdelijke samen voegingen
Het gebruikte geheugen (de grootte van de status) van een tijdelijke koppeling is evenredig met het aantal gebeurtenissen in de tijdelijke wiggle ruimte van de samen voeging. Dit is een invoer frequentie van gebeurtenissen vermenigvuldigd met de Wiggle-kamer grootte. Met andere woorden, het geheugen dat door samen voegingen wordt gebruikt, is evenredig met het tijd bereik van DateDiff vermenigvuldigd op basis van het gemiddelde gebeurtenis aantal.

Het aantal niet-overeenkomende gebeurtenissen in de samen voeging is van invloed op het geheugen gebruik voor de query. Met de volgende query wordt gezocht naar advertentieweergaven waarmee klikken worden gegenereerd:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

In dit voor beeld is het mogelijk dat er veel advertenties worden weer gegeven en dat er slechts enkele mensen op de reclame klikken. het is vereist dat alle gebeurtenissen in het tijd venster bewaard blijven. Het verbruikte geheugen is gerelateerd aan de venstergrootte en de snelheid waarmee gebeurtenissen elkaar opvolgen. 

Om dit op te lossen, moet u gebeurtenissen verzenden naar Event hub die is gepartitioneerd door de koppelings sleutels (ID in dit geval) en de query uitschalen door het systeem toe te staan elke invoer partitie afzonderlijk te verwerken met behulp  **van partitie** , zoals wordt weer gegeven:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

Wanneer de query is gepartitioneerd, wordt deze verspreid over verschillende knooppunten. Als gevolg hiervan wordt het aantal gebeurtenissen in elk knoop punt verminderd, waardoor de grootte van de status die in het deel venster toevoegen wordt bewaard, kleiner wordt. 

## <a name="temporal-analytic-functions"></a>Tijdelijke analyse functies
Het verbruikte geheugen (status grootte) van een tijdelijke analytische functie is evenredig met de gebeurtenis frequentie vermenigvuldigd met de duur. Het geheugen dat wordt gebruikt door de analytische functies, is niet evenredig met de venster grootte, maar in elk tijd venster wordt het aantal partities in de meeste gevallen gepartitioneerd.

Het herstel is vergelijkbaar met een tijdelijke samen voeging. U kunt de query uitschalen met behulp **van Partition by**. 

## <a name="out-of-order-buffer"></a>Buffer buiten bestelling 
Gebruiker kan de maximale buffer grootte configureren in het configuratie venster voor gebeurtenis ordening. De buffer wordt gebruikt om invoer voor de duur van het venster te bewaren en de volg orde te wijzigen. De grootte van de buffer is evenredig met de invoer frequentie van de gebeurtenis, vermenigvuldigd met de grootte van het buitenste volg orde venster. De standaard venster grootte is 0. 

Als u een overloop van de out-of-order buffer wilt herstellen, kunt u de query uitbreiden met behulp van **Partition by**. Wanneer de query is gepartitioneerd, wordt deze verspreid over verschillende knooppunten. Als gevolg hiervan wordt het aantal gebeurtenissen in elk knoop punt verminderd, waardoor het aantal gebeurtenissen in elke bestel buffer kleiner wordt. 

## <a name="input-partition-count"></a>Aantal invoer partities 
Elke invoer partitie van een taak invoer heeft een buffer. Hoe groter het aantal invoer partities, hoe meer resources de taak verbruikt. Voor elke streaming-eenheid kan Azure Stream Analytics ongeveer 1 MB/s aan invoer verwerken. Daarom kunt u optimaliseren door het aantal Stream Analytics streaming-eenheden te vergelijken met het aantal partities in de Event hub. 

Normaal gesp roken is een taak die is geconfigureerd met één streaming-eenheid voldoende voor een event hub met twee partities (de minimale waarde voor Event hub). Als de Event hub meer partities heeft, verbruikt uw Stream Analytics-taak meer resources, maar hoeft de extra door Voer van Event hub niet noodzakelijkerwijs te worden gebruikt. 

Voor een taak met 6 streaming-eenheden hebt u mogelijk 4 of 8 partities nodig van de Event hub. Vermijd echter dat er te veel onnodige partities zijn, omdat dit een overmatig gebruik van resources veroorzaakt. Bijvoorbeeld een event hub met 16 partities of groter in een Stream Analytics-taak met 1 streaming-eenheid. 

## <a name="reference-data"></a>Referentie gegevens 
Referentie gegevens in ASA worden in het geheugen geladen voor snel opzoeken. Met de huidige implementatie houdt elke samenvoegings bewerking met referentie gegevens een kopie van de referentie gegevens in het geheugen bij, zelfs als u meerdere keren met dezelfde referentie gegevens samenvoegt. Voor query's met een **partitie door** heeft elke partitie een kopie van de referentie gegevens, waardoor de partities volledig zijn losgekoppeld. Met het vermenigvuldigings effect kan het geheugen gebruik snel zeer hoog worden als u met meerdere partities meerdere keren met referentie gegevens werkt.  

### <a name="use-of-udf-functions"></a>Gebruik van UDF-functies
Wanneer u een UDF-functie toevoegt, wordt Azure Stream Analytics de Java Script-runtime in het geheugen geladen. Dit is van invloed op de SU%.

## <a name="next-steps"></a>Volgende stappen
* [Kan worden opgestart-query's maken in Azure Stream Analytics](stream-analytics-parallelization.md)
* [Azure Stream Analytics taken schalen om de door voer te verhogen](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png
