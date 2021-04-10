---
title: Spark Structured streaming in azure HDInsight
description: Het gebruik van Spark Structured streaming-toepassingen in HDInsight Spark-clusters.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: fd65177fb6202b0396545043c2e63a87c7f01bbb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864598"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Overzicht van Gestructureerde streaming van Apache Spark

[Apache Spark](https://spark.apache.org/) Met Structured streaming kunt u schaal bare en fout tolerante toepassingen implementeren voor het verwerken van gegevens stromen. Structured streaming is gebaseerd op de Spark SQL-engine en verbetert de constructies van Spark SQL-gegevens frames en gegevens sets, zodat u streaming query's op dezelfde manier kunt schrijven als batch-query's.  

Structured streaming-toepassingen worden uitgevoerd op HDInsight Spark-clusters en kunnen verbinding maken met streaminggegevens van [Apache Kafka](https://kafka.apache.org/), een TCP-socket (voor fout opsporing), Azure Storage of Azure data Lake Storage. De laatste twee opties, die afhankelijk zijn van externe opslag Services, bieden u de mogelijkheid om nieuwe bestanden die worden toegevoegd aan de opslag te bekijken en de inhoud ervan te verwerken alsof ze zijn gestreamd.

Structured streaming maakt een langlopende query waarbij u bewerkingen toepast op de invoer gegevens, zoals selectie, projectie, aggregatie, venster en het streamen van data frame met referentie DataFrames. Vervolgens voert u de resultaten uit naar bestands opslag (Azure Storage blobs of Data Lake Storage) of naar een Data Store met behulp van aangepaste code (zoals SQL Database of Power BI). Structured streaming biedt ook uitvoer naar de console voor het lokaal opsporen van fouten en naar een in-Memory tabel zodat u de gegevens kunt zien die zijn gegenereerd voor fout opsporing in HDInsight.

:::image type="content" source="./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png" alt-text="Stroom verwerking met HDInsight-en Spark Structured streaming" border="false":::

> [!NOTE]  
> Spark Structured streaming vervangt Spark-streaming (DStreams). Door te gaan, krijgen gestructureerde streaming meer verbeteringen en onderhoud, terwijl DStreams alleen in de onderhouds modus wordt uitgevoerd. Structured streaming is momenteel niet de functie-complete als DStreams voor de bronnen en sinks die het ondersteunt, dus Evalueer uw vereisten om de juiste optie voor de verwerking van Spark-stream te kiezen.

## <a name="streams-as-tables"></a>Stromen als tabellen

Met Spark Structured streaming wordt een gegevens stroom aangeduid als een tabel die niet is begrensd, dat wil zeggen, de tabel blijft groeien naarmate er nieuwe gegevens binnenkomen. Deze *invoer tabel* wordt continu verwerkt door een langlopende query en de resultaten die naar een *uitvoer tabel* worden verzonden:

:::image type="content" source="./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png" alt-text="Concept van Structured streaming" border="false":::

In structured streaming worden gegevens in het systeem bezorgd en onmiddellijk opgenomen in een invoer tabel. U schrijft query's (met behulp van de data frame-en DataSet-Api's) die bewerkingen uitvoeren op deze invoer tabel. De uitvoer van de query levert een andere tabel, de *resultaten tabel*. De resultaten tabel bevat de resultaten van de query waaruit u gegevens voor een extern gegevens archief, zoals een relationele data base, tekent. Het tijdstip waarop de gegevens worden verwerkt vanuit de invoer tabel, wordt bepaald door het *trigger interval*. Standaard is het trigger interval nul, zodat gestructureerde streaming probeert de gegevens te verwerken zodra deze binnenkomen. In de praktijk betekent dit dat zodra Structured streaming de uitvoering van de vorige query heeft verwerkt, een andere verwerkings uitvoering wordt gestart op basis van recent ontvangen gegevens. U kunt de trigger zo configureren dat deze wordt uitgevoerd met een interval, zodat de streaminggegevens worden verwerkt in op tijd gebaseerde batches.

De gegevens in de resultaten tabellen kunnen alleen de gegevens bevatten die nieuw zijn sinds de laatste keer dat de query is verwerkt (*toevoeg modus*), of de tabel kan worden vernieuwd telkens wanneer er nieuwe gegevens zijn, zodat de tabel alle uitvoer gegevens bevat, omdat de streaming-query is gestart (*modus volt ooien*).

### <a name="append-mode"></a>Toevoeg modus

In de toevoeg modus worden alleen de rijen die zijn toegevoegd aan de tabel met resultaten sinds de laatste query-uitvoering, aanwezig in de resultaten tabel en geschreven naar externe opslag. Zo kopieert de eenvoudigste query alleen alle gegevens uit de invoer tabel naar de tabel met resultaten. Telkens wanneer een trigger interval is verstreken, worden de nieuwe gegevens verwerkt en worden de rijen die de nieuwe gegevens vertegenwoordigen weer gegeven in de tabel met resultaten.

Overweeg een scenario waarin u telemetrie van temperatuur Sens oren, zoals een thermo staat, verwerkt. Stel dat de eerste trigger één gebeurtenis op tijdstip 00:01 voor apparaat 1 met een temperatuur meting van 95 graden heeft verwerkt. In de eerste trigger van de query wordt alleen de rij met tijd 00:01 weer gegeven in de tabel met resultaten. Op tijdstip 00:02 wanneer een andere gebeurtenis arriveert, is de enige nieuwe rij de rij met tijd 00:02. de resultaten tabel zou dus slechts één rij bevatten.

:::image type="content" source="./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png" alt-text="Toevoeg modus voor gestructureerde streaming" border="false":::

Wanneer u de toevoeg modus gebruikt, wordt er gebruikgemaakt van de gegevens (het selecteren van de kolommen waarmee de query wordt uitgevoerd), filteren (alleen rijen die aan bepaalde voor waarden voldoen) of samen voegen (waarbij de gegevens worden uitgebreid met gegevens uit een statische opzoek tabel). De toevoeg modus maakt het eenvoudig om alleen de relevante nieuwe gegevens punten naar externe opslag te pushen.

### <a name="complete-mode"></a>Volledige modus

U kunt hetzelfde scenario gebruiken. Dit is de tijd met de volledige modus. In de modus volledig wordt de hele uitvoer tabel vernieuwd op elke trigger zodat de tabel gegevens bevat die niet alleen uit de meest recente trigger worden uitgevoerd, maar van alle uitvoeringen. U kunt de modus volledig gebruiken om de niet-gewijzigde gegevens te kopiëren van de invoer tabel naar de resultaten tabel. Bij elke geactiveerde uitvoering worden de rijen met nieuwe resultaten samen met alle vorige rijen weer gegeven. De uitvoer resultaten tabel bevat alle gegevens die zijn verzameld sinds de query is gestart, en u hebt uiteindelijk te weinig geheugen. De modus volt ooien is bedoeld voor gebruik met statistische query's waarmee de inkomende gegevens op een bepaalde manier worden samengevat, zodat de resultaten tabel op elke trigger wordt bijgewerkt met een nieuwe samen vatting.

Er wordt van uitgegaan dat er vijf seconden aan gegevens zijn verwerkt en dat de gegevens voor de zesde seconde moeten worden verwerkt. De invoer tabel bevat gebeurtenissen voor tijd 00:01 en tijd 00:03. Het doel van deze voorbeeld query is om de gemiddelde Tempe ratuur van het apparaat om de vijf seconden te geven. De implementatie van deze query is van toepassing op een aggregatie waarbij alle waarden die binnen elk 5-Second venster vallen, het gemiddelde van de Tempe ratuur en een rij voor de gemiddelde Tempe ratuur van dat interval. Aan het einde van het eerste venster van 5 seconden zijn er twee Tuples: (00:01, 1, 95) en (00:03, 1, 98). Voor het venster 00:00-00:05 levert de aggregatie een tuple op met de gemiddelde Tempe ratuur van 96,5 graden. In het volgende 5-tweede venster is er slechts één gegevens punt op het moment 00:06, dus de resulterende gemiddelde Tempe ratuur is 98 graden. Op tijdstip 00:10, met behulp van de volledige modus, bevat de tabel met resultaten de rijen voor zowel Windows 00:00-00:05 als 00:05-00:10, omdat de query alle geaggregeerde rijen, niet alleen de nieuwe, oplevert. Daarom blijft de resultaten tabel groeien naarmate er nieuwe vensters worden toegevoegd.

:::image type="content" source="./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png" alt-text="Volledige modus voor gestructureerde streaming" border="false":::

Niet alle query's die gebruikmaken van de volledige modus, zorgen ervoor dat de tabel zonder grenzen groeit.  Bekijk in het vorige voor beeld dat in plaats van het gemiddelde van de Tempe ratuur per tijd venster het gemiddelde wordt berekend op basis van de apparaat-ID. De resultaat tabel bevat een vast aantal rijen (één per apparaat) met de gemiddelde Tempe ratuur voor het apparaat op alle gegevens punten die van dat apparaat zijn ontvangen. Wanneer er nieuwe temperaturen worden ontvangen, wordt de tabel met resultaten bijgewerkt zodat de gemiddelden in de tabel altijd actueel zijn.

## <a name="components-of-a-spark-structured-streaming-application"></a>Onderdelen van een Spark Structured streaming-toepassing

Met een eenvoudige voorbeeld query kunt u de Tempe ratuur-aflezingen per uur-lang Windows samenvatten. In dit geval worden de gegevens opgeslagen in JSON-bestanden in Azure Storage (gekoppeld als de standaard opslag voor het HDInsight-cluster):

```json
{"time":1469501107,"temp":"95"}
{"time":1469501147,"temp":"95"}
{"time":1469501202,"temp":"95"}
{"time":1469501219,"temp":"95"}
{"time":1469501225,"temp":"95"}
```

Deze JSON-bestanden worden opgeslagen in de `temps` submap onder de container van het HDInsight-cluster.

### <a name="define-the-input-source"></a>De invoer bron definiëren

Configureer eerst een data frame die de bron van de gegevens beschrijft en alle instellingen die nodig zijn voor die bron. In dit voor beeld wordt van de JSON-bestanden in Azure Storage getekend en wordt er op Lees tijden een schema toegepast op het bestand.

```sql
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

//Cluster-local path to the folder containing the JSON files
val inputPath = "/temps/" 

//Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

//Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath)
``` 

#### <a name="apply-the-query"></a>De query Toep assen

Pas vervolgens een query toe die de gewenste bewerkingen voor de streaming data frame bevat. In dit geval worden alle rijen in één uur in Windows gegroepeerd en worden de minimale, gemiddelde en maximum temperaturen in het venster van 1 uur berekend.

```sql
val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))
```

### <a name="define-the-output-sink"></a>De uitvoer-Sink definiëren

Definieer vervolgens de bestemming voor de rijen die worden toegevoegd aan de resultaten tabel binnen elk trigger interval. In dit voor beeld worden alleen alle rijen uitgevoerd naar een in-Memory tabel `temps` die u later kunt opvragen met SparkSQL. Volledige uitvoer modus zorgt ervoor dat alle rijen voor alle vensters elke keer worden uitgevoerd.

```sql
val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete")
``` 

### <a name="start-the-query"></a>De query starten

Start de streaming-query en voer uit totdat een beëindigings signaal wordt ontvangen.

```sql
val query = streamingOutDF.start() 
``` 

### <a name="view-the-results"></a>De resultaten bekijken

Terwijl de query wordt uitgevoerd, kunt u in dezelfde SparkSession een SparkSQL-query uitvoeren op de `temps` tabel waarin de query resultaten worden opgeslagen.

```sql
select * from temps
```

Deze query levert resultaten op die vergelijkbaar zijn met de volgende:

| venster |  min (tijdelijk) | Gem (tijdelijk) | Max (tijdelijk) |
| --- | --- | --- | --- |
|{u'start ': u ' 2016-07-26T02:00:00.000 Z ', u'end '... |    95 |    95,231579 | 99 |
|{u'start ': u ' 2016-07-26T03:00:00.000 Z ', u'end '...  |95 |   96,023048 | 99 |
|{u'start ': u ' 2016-07-26T04:00:00.000 Z ', u'end '...  |95 |   96,797133 | 99 |
|{u'start ': u ' 2016-07-26T05:00:00.000 Z ', u'end '...  |95 |   96,984639 | 99 |
|{u'start ': u ' 2016-07-26T06:00:00.000 Z ', u'end '...  |95 |   97,014749 | 99 |
|{u'start ': u ' 2016-07-26T07:00:00.000 Z ', u'end '...  |95 |   96,980971 | 99 |
|{u'start ': u ' 2016-07-26T08:00:00.000 Z ', u'end '...  |95 |   96,965997 | 99 |  

Zie [Apache Spark Structured streaming Programming Guide (Engelstalig](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)) voor meer informatie over de Spark Structured stream API, samen met de invoer gegevens bronnen, bewerkingen en uitvoer-sinks die worden ondersteund.

## <a name="checkpointing-and-write-ahead-logs"></a>Controle punten en write-Ahead logboeken

Voor het leveren van tolerantie en fout tolerantie is gestructureerde streaming afhankelijk van de *controle punten* om ervoor te zorgen dat de stroom verwerking zonder onderbrekingen kan worden voortgezet, zelfs als er knooppunt fouten optreden. In HDInsight maakt Spark controle punten aan duurzame opslag, hetzij Azure Storage als Data Lake Storage. Deze controle punten slaan de voortgangs informatie over de streaming-query op. Daarnaast maakt Structured streaming gebruik van een *Write-Ahead logboek* (Wal). De WAL legt opgenomen gegevens vast die zijn ontvangen, maar nog niet zijn verwerkt door een query. Als er een fout optreedt en de verwerking opnieuw wordt gestart vanuit de WAL, gaan alle gebeurtenissen die van de bron worden ontvangen, verloren.

## <a name="deploying-spark-streaming-applications"></a>Toepassingen voor Spark-Streaming implementeren

Doorgaans bouwt u een Spark-streaming-toepassing lokaal in een JAR-bestand en implementeert u deze vervolgens in Spark op HDInsight door het JAR-bestand te kopiëren naar de standaard opslag die aan uw HDInsight-cluster is gekoppeld. U kunt uw toepassing starten met de [Apache livy](https://livy.incubator.apache.org/) rest api's die beschikbaar zijn via uw cluster met behulp van een post-bewerking. De hoofd tekst van het bericht bevat een JSON-document met het pad naar uw JAR, de naam van de klasse waarvan de methode main de streaming-toepassing definieert en uitvoert, en optioneel de resource vereisten van de taak (zoals het aantal uitvoerendeers, het geheugen en de kernen) en alle configuratie-instellingen die uw toepassings code vereist.

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png" alt-text="Een Spark streaming-toepassing implementeren" border="false":::

De status van alle toepassingen kan ook worden gecontroleerd met een GET-aanvraag voor een LIVY-eind punt. Ten slotte kunt u een actieve toepassing beëindigen door een DELETE-aanvraag uit te geven voor het LIVY-eind punt. Zie [externe taken met Apache LIVY](apache-spark-livy-rest-interface.md) voor meer informatie over de LIVY-API

## <a name="next-steps"></a>Volgende stappen

* [Een Apache Spark-cluster maken in Azure HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Programmeer gids voor Apache Spark Structured streaming](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Apache Spark-taken extern starten met Apache LIVY](apache-spark-livy-rest-interface.md)
