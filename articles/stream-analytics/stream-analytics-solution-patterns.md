---
title: Azure Stream Analytics-oplossingspatronen
description: Meer informatie over algemene oplossings patronen voor Azure Stream Analytics, zoals Dash boards, gebeurtenis berichten, gegevens archieven, verrijkingen van referentie gegevens en bewaking.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 1bd3c1099344bd266d7e3bc153613daaecfb412a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020312"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Azure Stream Analytics-oplossingspatronen

Net als bij veel andere services in azure kunt u Stream Analytics het beste met andere services gebruiken om een grotere end-to-end oplossing te maken. In dit artikel worden eenvoudige Azure Stream Analytics oplossingen en verschillende architecturale patronen besproken. U kunt deze patronen bouwen om complexere oplossingen te ontwikkelen. De patronen die in dit artikel worden beschreven, kunnen worden gebruikt in een groot aantal verschillende scenario's. Voor beelden van scenario-specifieke patronen zijn beschikbaar in [Azure-oplossings architecturen](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics).

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Een Stream Analytics taak maken om in realtime Dash boards te kunnen werken

Met Azure Stream Analytics kunt u snel realtime-Dash boards en-waarschuwingen opschonen. Met een eenvoudige oplossing worden gebeurtenissen van Event hubs of IOT hub opgenomen, en wordt [het Power bi dash board met een gegevensverzamelinggegevens gevoed](/power-bi/service-real-time-streaming). Zie voor meer informatie de gedetailleerde zelf studie voor het [analyseren van frauduleuze gegevens met Stream Analytics en het visualiseren van resultaten in Power bi dash board](stream-analytics-real-time-fraud-detection.md).

![ASA Power BI-dash board](media/stream-analytics-solution-patterns/power-bi-dashboard.png)

Deze oplossing kan in slechts enkele minuten van Azure Portal worden gebouwd. Er is geen uitgebreide code ring betrokken en SQL-taal wordt gebruikt voor het uitdrukken van de bedrijfs logica.

Dit oplossings patroon biedt de laagste latentie van de gebeurtenis bron naar het Power BI dash board in een browser. Azure Stream Analytics is de enige Azure-service met deze ingebouwde mogelijkheid.

## <a name="use-sql-for-dashboard"></a>SQL voor dash board gebruiken

Het Power BI dash board biedt lage latentie, maar kan niet worden gebruikt voor het produceren van volledige Power BI rapporten. Een algemeen rapportage patroon is het uitvoeren van uw gegevens naar SQL Database eerst. Gebruik vervolgens de SQL-connector van Power BI om SQL voor de meest recente gegevens op te vragen.

![ASA SQL-dash board](media/stream-analytics-solution-patterns/sql-dashboard.png)

Het gebruik van SQL Database biedt meer flexibiliteit, maar ten koste van een iets hogere latentie. Deze oplossing is optimaal voor taken met een latentie vereisten van meer dan één seconde. Met deze methode kunt u de mogelijkheden van Power BI optimaliseren om de gegevens voor rapporten verder te segmenteren en te dobbelten, en nog veel meer visualisatie opties. U krijgt ook de flexibiliteit om andere dashboard oplossingen te gebruiken, zoals tableau.

SQL is geen gegevens opslag met hoge door voer. De maximale door voer naar SQL Database van Azure Stream Analytics is momenteel ongeveer 24 MB/s. Als de gebeurtenis bronnen in uw oplossing een hoger aantal gegevens produceren, moet u de verwerkings logica in Stream Analytics gebruiken om de uitvoer snelheid naar SQL te verminderen. Technieken zoals filteren, aggregaties in Vensters, patroon vergelijking met tijdelijke samen voegingen en analyse functies kunnen worden gebruikt. De uitvoer frequentie naar SQL kan verder worden geoptimaliseerd met behulp van technieken die worden beschreven in [Azure stream Analytics uitvoer naar Azure SQL database](stream-analytics-sql-output-perf.md).

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Real-time inzichten opnemen in uw toepassing met gebeurtenis berichten

Het tweede meest populaire gebruik van Stream Analytics is het genereren van real-time waarschuwingen. In dit oplossings patroon kan bedrijfs logica in Stream Analytics worden gebruikt voor het detecteren van [tijdelijke en ruimtelijke patronen](stream-analytics-geospatial-functions.md) of [afwijkingen](stream-analytics-machine-learning-anomaly-detection.md), waarna waarschuwings signalen worden gegenereerd. Maar in tegens telling tot de dashboard oplossing waarbij Stream Analytics Power BI als voorkeurs eindpunt gebruikt, kan een aantal tussenliggende gegevens-sinks worden gebruikt. Deze sinks zijn Event Hubs, Service Bus en Azure Functions. Als de opbouw functie voor toepassingen moet u bepalen welke gegevens Sink het meest geschikt is voor uw scenario.

Er moeten downstream event consumer Logic worden geïmplementeerd voor het genereren van waarschuwingen in uw bestaande zakelijke werk stroom. Omdat u aangepaste logica in Azure Functions kunt implementeren, is Azure Functions de snelste manier om deze integratie uit te voeren. Een zelf studie voor het gebruik van Azure function als uitvoer voor een Stream Analytics-taak vindt u in [Run Azure functions van Azure stream Analytics Jobs](stream-analytics-with-azure-functions.md). Azure Functions ondersteunt ook verschillende typen meldingen, waaronder tekst en e-mail. Logische apps kunnen ook worden gebruikt voor deze integratie, met Event Hubs tussen Stream Analytics en logische apps.

![Event Messa ging-app voor ASA](media/stream-analytics-solution-patterns/event-messaging-app.png)

Event Hubs biedt daarentegen het meest flexibele integratie punt. Veel andere services, zoals Azure Data Explorer en Time Series Insights, kunnen gebeurtenissen van Event Hubs gebruiken. Services kunnen rechtstreeks worden verbonden met de Event Hubs-Sink van Azure Stream Analytics om de oplossing te volt ooien. Event Hubs is ook de hoogste doorvoer bericht Broker die beschikbaar is op Azure voor dergelijke integratie scenario's.

## <a name="dynamic-applications-and-websites"></a>Dynamische toepassingen en websites

U kunt aangepaste realtime visualisaties maken, zoals dash board of kaart visualisatie, met behulp van Azure Stream Analytics en de Azure signalerings service. Met Signa lering kunnen webclients worden bijgewerkt en dynamische inhoud in realtime worden weer gegeven.

![ASA dynamische app](media/stream-analytics-solution-patterns/dynamic-app.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Real-time inzichten opnemen in uw toepassing via gegevens archieven

De meeste webservices en webtoepassingen gebruiken vandaag nog een aanvraag/antwoord patroon om de presentatielaag te leveren. Het patroon van de aanvraag/reactie is eenvoudig te bouwen en kan eenvoudig worden geschaald met een lage reactie tijd met een stateless front-end en schaal bare winkels, zoals Cosmos DB.

High data volume maakt vaak knel punten in een systeem op basis van prestaties. Het [oplossings patroon gebeurtenis bronnen](/azure/architecture/patterns/event-sourcing) wordt gebruikt om de knel punten in de prestaties op te lossen. Tijdelijke patronen en inzichten zijn ook lastig en moeilijk te extra heren uit een traditionele gegevens opslag. Moderne grootschalige toepassingen voor gegevens gebruik nemen vaak een architectuur op basis van een gegevensfeed. Azure Stream Analytics als Compute-engine voor gegevens in beweging is een spil in die architectuur.

![Event sourcing-app voor ASA](media/stream-analytics-solution-patterns/event-sourcing-app.png)

In dit oplossings patroon worden gebeurtenissen verwerkt en geaggregeerd in gegevens archieven door Azure Stream Analytics. De toepassingslaag communiceert met gegevens archieven met behulp van het traditionele patroon van aanvraag/antwoord. Vanwege Stream Analytics ' de mogelijkheid om een groot aantal gebeurtenissen in realtime te verwerken, is de toepassing uiterst schaalbaar zonder dat de laag van het gegevensarchief hoeft te worden opgedeeld. De laag van het gegevens archief is in feite een gerealiseerde weer gave in het systeem. [Azure stream Analytics uitvoer naar Azure Cosmos DB](stream-analytics-documentdb-output.md) wordt beschreven hoe Cosmos DB wordt gebruikt als stream Analytics uitvoer.

In echte toepassingen waarbij de verwerkings logica complex is en de nood zaak is om bepaalde delen van de logica afzonderlijk bij te werken, kunnen meerdere Stream Analytics taken samen met Event Hubs worden samengesteld als de tussenliggende gebeurtenis Broker.

![ASA-app met complexe gebeurtenis bronnen](media/stream-analytics-solution-patterns/event-sourcing-app-complex.png)

Dit patroon verbetert de flexibiliteit en beheer baarheid van het systeem. Hoewel Stream Analytics echter precies één keer zeker weet dat er sprake is van verwerking, is er een klein risico dat dubbele gebeurtenissen in de intermediair worden geEvent Hubs. Het is belang rijk dat de stroomafwaartse Stream Analytics-taak gebeurtenissen ontdubbelt met behulp van logische sleutels in een lookback-venster. Zie voor meer informatie over de levering van gebeurtenissen [gebeurtenis leverings garanties](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) .

## <a name="use-reference-data-for-application-customization"></a>Referentie gegevens gebruiken voor het aanpassen van toepassingen

De functie voor het Azure Stream Analytics referentie gegevens is specifiek ontworpen voor aanpassing aan eind gebruikers, zoals waarschuwings drempelwaarde, verwerkings regels en [geofences](geospatial-scenarios.md). De toepassingslaag kan parameter wijzigingen accepteren en in SQL Database opslaan. Met de Stream Analytics taak wordt periodiek een query uitgevoerd op wijzigingen in de data base en worden de aanpassings parameters toegankelijk via een referentie gegevens koppeling. Voor meer informatie over het gebruik van referentie gegevens voor het aanpassen van toepassingen raadpleegt u [SQL-referentie gegevens](sql-reference-data.md) en [samen voegen met referentie gegevens](/stream-analytics-query/reference-data-join-azure-stream-analytics).

Dit patroon kan ook worden gebruikt voor het implementeren van een regel engine waarbij de drempel waarden van de regels worden gedefinieerd vanuit referentie gegevens. Zie voor meer informatie over regels de [procedure Configureer bare op drempel waarden gebaseerde regels in azure stream Analytics](stream-analytics-threshold-based-rules.md).

![App voor referentie gegevens van ASA](media/stream-analytics-solution-patterns/reference-data-app.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Machine Learning toevoegen aan uw real-time inzichten

Azure Stream Analytics ' ingebouwd [anomalie detectie model](stream-analytics-machine-learning-anomaly-detection.md) ' is een handige manier om machine learning te introduceren in uw realtime-toepassing. Zie [Azure stream Analytics integreren met de Score service van Azure machine learning](stream-analytics-machine-learning-integration-tutorial.md)voor een groter aantal machine learning behoeften.

Zie dit voor beeld van een [lineaire regressie](stream-analytics-high-frequency-trading.md)voor ervaren gebruikers die online trainingen en scores willen opnemen in dezelfde stream Analytics pijp lijn.

![ASA Machine Learning-app](media/stream-analytics-solution-patterns/machine-learning-app.png)

## <a name="real-time-data-warehousing"></a>Real-time gegevens opslag

Een ander algemeen patroon is real-time gegevens opslag, ook wel streaming data warehouse genoemd. Naast gebeurtenissen die op Event Hubs en IoT Hub uit uw toepassing arriveren, kunnen [Azure stream Analytics die op IOT Edge wordt uitgevoerd](stream-analytics-edge.md) , worden gebruikt om te voldoen aan het opschonen van gegevens, gegevens reductie en gegevens opslag en doorstuur behoeften. Stream Analytics die op IoT Edge wordt uitgevoerd, kunnen de bandbreedte limiet en verbindings problemen in het systeem op de juiste manier verwerken. Stream Analytics kunnen doorvoer tarieven van Maxi maal 200 MB per seconde ondersteunen tijdens het schrijven naar Azure Synapse Analytics.

![ASA data warehousing](media/stream-analytics-solution-patterns/data-warehousing.png)


## <a name="archiving-real-time-data-for-analytics"></a>Real-time gegevens archiveren voor analyse

De meeste data wetenschappen-en analyse activiteiten worden nog offline uitgevoerd. Gegevens kunnen worden gearchiveerd door Azure Stream Analytics Azure Data Lake Store Gen2 uitvoer en Parquet. Deze mogelijkheid verwijdert de wrijving tot gegevens rechtstreeks in Azure Data Lake Analytics, Azure Databricks en Azure HDInsight. Azure Stream Analytics wordt gebruikt als een bijna realtime ETL-engine in deze oplossing. U kunt gearchiveerde gegevens in Data Lake verkennen met behulp van verschillende reken engines.

> [!div class="mx-imgBorder"]
> ![ASA offline-analyse](media/stream-analytics-solution-patterns/offline-analytics.png)

## <a name="use-reference-data-for-enrichment"></a>Referentie gegevens gebruiken voor verrijking

Gegevens verrijking is vaak een vereiste voor ETL-engines. Azure Stream Analytics ondersteunt gegevens verrijking met [referentie gegevens](stream-analytics-use-reference-data.md) van zowel SQL database als Azure Blob-opslag. Gegevens verrijking kan worden uitgevoerd voor de aanvoer van gegevens in zowel Azure Data Lake als Azure Synapse Analytics.


![ASA offline analyse met gegevens verrijking](media/stream-analytics-solution-patterns/offline-analytics-enriched.png)

## <a name="operationalize-insights-from-archived-data"></a>Operationeel maken Insights van gearchiveerde gegevens

Als u het offline analyse patroon combineert met het bijna realtime toepassings patroon, kunt u een feedback-lus maken. Met de feedback-lus kan de toepassing automatisch worden aangepast voor het wijzigen van patronen in de gegevens. Deze feedback-lus kan net zo eenvoudig zijn als het wijzigen van de drempel waarde voor waarschuwingen of als complex als het opnieuw trainen van Machine Learning modellen. Dezelfde oplossings architectuur kan worden toegepast op beide ASA-taken die worden uitgevoerd in de Cloud en op IoT Edge.

![ASA Insights-uitoefening](media/stream-analytics-solution-patterns/insights-operationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>ASA-taken bewaken

Een Azure Stream Analytics taak kan 24/7 worden uitgevoerd om binnenkomende gebeurtenissen in realtime continu te verwerken. De gegarandeerde uptime is van cruciaal belang voor de status van de volledige toepassing. Hoewel Stream Analytics de enige streaming Analytics-service in de branche is die een  [gegarandeerde Beschik baarheid van 99,9%](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)biedt, is er mogelijk nog steeds een zekere mate van tijd. Stream Analytics heeft gedurende de jaren metrische gegevens, logboeken en taak statussen geïntroduceerd om de status van de taken weer te geven. Deze zijn allemaal zichtbaar via Azure Monitor-service en kunnen verder worden geëxporteerd naar OMS. Zie [informatie over stream Analytics taak bewaking en het controleren van query's](stream-analytics-monitoring.md)voor meer informatie.

![ASA-bewaking](media/stream-analytics-solution-patterns/monitoring.png)

Er zijn twee belang rijke dingen om te bewaken:

- [Status van mislukte taak](job-states.md)

    U moet er eerst voor zorgen dat de taak wordt uitgevoerd. Zonder dat de taak wordt uitgevoerd, worden er geen nieuwe metrische gegevens of logboeken gegenereerd. Taken kunnen om verschillende redenen worden gewijzigd in een mislukte status, waaronder het gebruik van een hoog SU-gebruiks niveau (dat wil zeggen te weinig resources).

- [Meet waarden voor watermerk vertraging](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Met deze metriek wordt aangegeven hoe ver achter uw verwerkings pijplijn de klok tijd van de wand (seconden) is. Een deel van de vertraging wordt toegeschreven aan de logica voor de inherente verwerking. Als gevolg hiervan is het controleren van de toenemende trend veel belang rijker dan het bewaken van de absolute waarde. De stabiele status vertraging moet worden opgelost door het ontwerp van uw toepassing, niet door bewaking of waarschuwingen.

Als er een fout is opgetreden, zijn de logboeken voor activiteiten en [Diagnostische gegevens](stream-analytics-job-diagnostic-logs.md) de beste locaties om te beginnen met het zoeken naar fouten.

## <a name="build-resilient-and-mission-critical-applications"></a>Robuuste en essentiële toepassingen bouwen

Ongeacht de SLA-garantie van Azure Stream Analytics en hoe voorzichtig u uw end-to-end-toepassing uitvoert, treedt er een storing op. Als uw toepassing bedrijfs kritiek is, moet u voor bereid zijn op storingen om op de juiste wijze te herstellen.

Voor het melden van toepassingen is het belang rijk dat u de volgende waarschuwing detecteert. U kunt ervoor kiezen om de taak opnieuw te starten vanaf de huidige tijd bij het herstellen, waarbij eerdere waarschuwingen worden genegeerd. De tijd voor het starten van de taak is de eerste uitvoer tijd, niet de eerste invoer tijd. De invoer wordt terugspoelen, een juiste hoeveelheid tijd om te garanderen dat de eerste uitvoer op de opgegeven tijd volledig en correct is. Als gevolg hiervan worden geen gedeeltelijke aggregaties opgehaald en wordt onverwacht waarschuwingen gegenereerd.

U kunt er ook voor kiezen om de uitvoer van een bepaalde tijd in het verleden te starten. Zowel Event Hubs als het Bewaar beleid van IoT Hub bevatten een redelijke hoeveelheid gegevens om de verwerking vanuit het verleden toe te staan. De balans is hoe snel u de huidige tijd kunt opvangen en u begint met het genereren van tijdige nieuwe waarschuwingen. Gegevens verliezen de waarde snel in de loop van de tijd. het is dus belang rijk om snel aan de slag te gaan met de huidige tijd. Er zijn twee manieren om snel op te vangen:

- Meer bronnen (SU) inrichten bij het opvangen van.
- Opnieuw opstarten vanaf de huidige tijd.

Opnieuw opstarten vanaf de huidige tijd is eenvoudig, met het bebalans van een onderbreking tijdens de verwerking. Het opnieuw opstarten van deze manier kan op de hoogte zijn van waarschuwings scenario's, maar kan problemen opleveren voor dashboard scenario's en is een niet-starter voor het archiveren van gegevens en Data Warehouse-scenario's.

Het inrichten van meer resources kan het proces versnellen, maar het effect van een verwerkings snelheid is complex.

- Test of uw taak schaalbaar is tot een groter aantal SUs. Niet alle query's zijn schaalbaar. U moet controleren of uw query is [geparallelleerd](stream-analytics-parallelization.md).

- Zorg ervoor dat de upstream-Event Hubs voldoende partities bevat of IoT Hub u meer doorvoer eenheden (TUs) kunt toevoegen om de invoer doorvoer te schalen. Houd er rekening mee dat elke Event Hubs TU dergelijke bij een uitvoer snelheid van 2 MB/s.

- Zorg ervoor dat u voldoende resources hebt ingericht in de uitvoer-Sinks (SQL Database, Cosmos DB), zodat ze de piek in uitvoer niet kunnen beperken, wat soms ertoe kan leiden dat het systeem wordt vergrendeld.

Het belangrijkste is dat u de wijziging van de verwerkings factor moet anticiperen, deze scenario's kunt testen voordat u naar de productie omgeving gaat en de verwerking op de juiste manier kunt schalen tijdens de herstel tijd van de storing.

In het extreme scenario dat binnenkomende gebeurtenissen allemaal worden vertraagd, kunnen [alle vertraagde gebeurtenissen worden verwijderd](stream-analytics-time-handling.md) als u een laat tijdige periode hebt toegepast op uw taak. Het verwijderen van de gebeurtenissen lijkt aan het begin van een verwarrende-gedrag te zijn. het overwegen van Stream Analytics echter een real-time verwerkings engine is, verwacht dan dat binnenkomende gebeurtenissen dicht bij de klok tijd van de wand moeten liggen. Het moet gebeurtenissen weghalen die deze beperkingen schenden.

### <a name="lambda-architectures-or-backfill-process"></a>Lambda-architecturen of backfill-proces

Gelukkig kan het vorige patroon voor het archiveren van gegevens worden gebruikt om deze late gebeurtenissen op de juiste wijze te verwerken. Het idee is dat de archiverings taak binnenkomende gebeurtenissen in aankomst tijd verwerkt en gebeurtenissen archiveert in het juiste tijds interval van Azure Blob of Azure Data Lake Store met de bijbehorende gebeurtenis tijd. Het maakt niet uit hoe laat een gebeurtenis arriveert, maar wordt nooit verwijderd. Het land wordt altijd in het juiste tijds interval gegrond. Tijdens het herstel is het mogelijk om de gearchiveerde gebeurtenissen opnieuw te verwerken en de resultaten te backfill naar de gewenste opslag locatie. Dit is vergelijkbaar met de manier waarop Lambda-patronen worden geïmplementeerd.

![ASA backfill](media/stream-analytics-solution-patterns/back-fill.png)

Het backfill-proces moet worden uitgevoerd met een offline batch verwerkings systeem, wat waarschijnlijk een ander programmeer model heeft dan Azure Stream Analytics. Dit betekent dat u de volledige verwerkings logica opnieuw moet implementeren.

Voor backfilling is het nog steeds belang rijk om ten minste tijdelijk meer bronnen in te richten op de output-sinks voor een hogere door voer dan de verwerkings behoeften van de stabiele status.

|Scenario's  |Opnieuw opstarten vanaf nu  |Opnieuw opstarten na laatste keer gestopt |Opnieuw opstarten vanaf nu + backfill met gearchiveerde gebeurtenissen|
|---------|---------|---------|---------|
|**Dash boards**   |Maakt tussen ruimte    |OK voor korte onderbreking    |Gebruiken voor lange onderbreking |
|**Waarschuwingen**   |Aanvaardbaar |OK voor korte onderbreking    |Niet nodig |
|**App voor gebeurtenis bronnen** |Aanvaardbaar |OK voor korte onderbreking    |Gebruiken voor lange onderbreking |
|**Gegevens opslag**   |Gegevensverlies  |Aanvaardbaar |Niet nodig |
|**Offline analyse**  |Gegevensverlies  |Aanvaardbaar |Niet nodig|

## <a name="putting-it-all-together"></a>Alles samenvoegen

Het is niet moeilijk om te Voorst Ellen dat alle hierboven genoemde oplossings patronen samen in een complex end-to-end-systeem kunnen worden gecombineerd. Het gecombineerde systeem kan Dash boards, waarschuwingen, gebeurtenis bronnen toepassing, gegevens opslag en mogelijkheden voor offline analyse bevatten.

De sleutel is het ontwerpen van uw systeem in samenstel bare patronen, zodat elk subsysteem kan worden gebouwd, getest, bijgewerkt en onafhankelijk kan worden hersteld.

## <a name="next-steps"></a>Volgende stappen

U hebt nu diverse oplossings patronen gezien met behulp van Azure Stream Analytics. Hierna kunt u zich verder in de materie verdiepen en uw eerste Stream Analytics-taak maken:

* [Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md).
* [Create a Stream Analytics job by using Azure PowerShell](stream-analytics-quick-create-powershell.md) (Een Stream Analytics-taak maken met behulp van Azure PowerShell).
* [Create a Stream Analytics job by using Visual Studio](stream-analytics-quick-create-vs.md) (Een Stream Analytics-taak maken met behulp van Visual Studio).
