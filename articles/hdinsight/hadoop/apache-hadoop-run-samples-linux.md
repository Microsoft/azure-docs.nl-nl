---
title: Apache Hadoop MapReduce-voor beelden uitvoeren op HDInsight-Azure
description: Aan de slag met MapReduce-voor beelden in JAR-bestanden die zijn opgenomen in HDInsight. Gebruik SSH om verbinding te maken met het cluster en gebruik vervolgens de Hadoop-opdracht om voorbeeld taken uit te voeren.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/12/2019
ms.openlocfilehash: 5e75a39b1f9e8503097914b0c9e735915f9ae667
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943216"
---
# <a name="run-the-mapreduce-examples-included-in-hdinsight"></a>De MapReduce-voor beelden uit HDInsight uitvoeren

[!INCLUDE [samples-selector](../../../includes/hdinsight-run-samples-selector.md)]

Meer informatie over het uitvoeren van de MapReduce-voor beelden die zijn opgenomen in Apache Hadoop op HDInsight.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="the-mapreduce-examples"></a>De MapReduce-voor beelden

De voor beelden bevinden zich in het HDInsight-cluster op `/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar` . De bron code voor deze steek proeven is opgenomen in het HDInsight-cluster op `/usr/hdp/current/hadoop-client/src/hadoop-mapreduce-project/hadoop-mapreduce-examples` .

De volgende voor beelden zijn opgenomen in dit archief:

|Voorbeeld |Beschrijving |
|---|---|
|aggregatewordcount|Telt de woorden in de invoer bestanden.|
|aggregatewordhist|Hiermee berekent u het histogram van de woorden in de invoer bestanden.|
|bbp|Maakt gebruik van Bailey-Borwein-Plouffe voor het berekenen van exacte cijfers pi.|
|dbcount|Telt de pagina weergave-logboeken die in een Data Base zijn opgeslagen.|
|distbbp|Maakt gebruik van een BBP formule voor het berekenen van exacte bits pi.|
|grep|Telt de treffers van een regex in de invoer.|
|join|Voert een koppeling uit op gesorteerde, gelijkmatig gepartitioneerde gegevens sets.|
|multifilewc|Telt woorden uit verschillende bestanden.|
|pentomino|Tegel programma voor het vinden van oplossingen voor pentomino-problemen.|
|IP|Schat Pi met behulp van een quasi-Monte Carlo-methode.|
|randomtextwriter|Schrijft 10 GB aan wille keurige tekstuele gegevens per knoop punt.|
|randomwriter|Schrijft 10 GB aan wille keurige gegevens per knoop punt.|
|secondarysort|Hiermee definieert u een secundaire sortering op de reductie fase.|
|sorteren|Hiermee worden de gegevens gesorteerd die door de wille keurige schrijver zijn geschreven.|
|sudoku|Een Sudoku-Oplosser.|
|teragen|Gegevens voor de terasort genereren.|
|terasort|Voer de terasort uit.|
|teravalidate|De resultaten van terasort controleren.|
|WordCount|Telt de woorden in de invoer bestanden.|
|wordmean|Telt de gemiddelde lengte van de woorden in de invoer bestanden.|
|wordmedian|Telt de mediaan lengte van de woorden in de invoer bestanden.|
|wordstandarddeviation|Telt de standaard afwijking van de lengte van de woorden in de invoer bestanden.|

## <a name="run-the-wordcount-example"></a>Het WordCount-voor beeld uitvoeren

1. Verbinding maken met HDInsight via SSH. Vervang door `CLUSTER` de naam van uw cluster en voer de volgende opdracht in:

    ```cmd
    ssh sshuser@CLUSTER-ssh.azurehdinsight.net
    ```

2. Gebruik in de SSH-sessie de volgende opdracht om de voor beelden weer te geven:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar
    ```

    Met deze opdracht wordt de lijst met voor beelden uit de vorige sectie van dit document gegenereerd.

3. Gebruik de volgende opdracht om hulp te krijgen voor een specifiek voor beeld. In dit geval is het **WordCount** -voor beeld:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount
    ```

    Het volgende bericht wordt weer gegeven:

    ```output
    Usage: wordcount <in> [<in>...] <out>
    ```

    Dit bericht geeft aan dat u verschillende invoer paden voor de bron documenten kunt opgeven. Het laatste pad is de locatie waar de uitvoer (aantal woorden in de bron documenten) wordt opgeslagen.

4. Gebruik de volgende informatie om alle woorden in de notebooks van Leonardo da Vinci te tellen, die als voorbeeld gegevens worden meegeleverd met uw cluster:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount
    ```

    Invoer voor deze taak is gelezen uit `/example/data/gutenberg/davinci.txt` . De uitvoer van dit voor beeld is opgeslagen in `/example/data/davinciwordcount` . Beide paden bevinden zich in de standaard opslag voor het cluster, niet op het lokale bestands systeem.

   > [!NOTE]  
   > Zoals vermeld in de Help voor het WordCount-voor beeld, kunt u ook meerdere invoer bestanden opgeven. `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount`Telt bijvoorbeeld woorden in zowel davinci.txt als ulysses.txt.

5. Wanneer de taak is voltooid, gebruikt u de volgende opdracht om de uitvoer weer te geven:

    ```bash
    hdfs dfs -cat /example/data/davinciwordcount/*
    ```

    Met deze opdracht worden alle uitvoer bestanden die door de taak worden geproduceerd, samengevoegd. De uitvoer naar de-console wordt weer gegeven. De uitvoer lijkt op het volgende:

    ```output
    zum     1
    zur     1
    zwanzig 1
    zweite  1
    ```

    Elke regel vertegenwoordigt een woord en hoe vaak het is opgetreden in de invoer gegevens.

## <a name="the-sudoku-example"></a>Het Sudoku-voor beeld

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) is een Logic puzzel die bestaat uit negen 3x3 rasters. Sommige cellen in het raster hebben cijfers, terwijl andere leeg zijn, en het doel is om de lege cellen op te lossen. De vorige koppeling bevat meer informatie over de puzzel, maar het doel van dit voor beeld is om een oplossing voor de lege cellen te maken. Daarom moet de invoer een bestand zijn met de volgende indeling:

* Negen rijen van negen kolommen
* Elke kolom kan een getal bevatten of `?` (waarmee een lege cel wordt aangegeven)
* Cellen worden gescheiden door een spatie

Er is een bepaalde manier om Sudoku-puzzels samen te stellen. u kunt een getal in een kolom of rij niet herhalen. Er is een voor beeld van het HDInsight-cluster dat op de juiste manier is gebouwd. Deze bevindt zich in `/usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta` en bevat de volgende tekst:

```output
8 5 ? 3 9 ? ? ? ?
? ? 2 ? ? ? ? ? ?
? ? 6 ? 1 ? ? ? 2
? ? 4 ? ? 3 ? 5 9
? ? 8 9 ? 1 4 ? ?
3 2 ? 4 ? ? 8 ? ?
9 ? ? ? 8 ? 5 ? ?
? ? ? ? ? ? 2 ? ?
? ? ? ? 4 5 ? 7 8
```

Als u dit voorbeeld probleem wilt uitvoeren via het Sudoku-voor beeld, gebruikt u de volgende opdracht:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/*/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta
```

De resultaten lijken op de volgende tekst:

```output
8 5 1 3 9 2 6 4 7
4 3 2 6 7 8 1 9 5
7 9 6 5 1 4 3 8 2
6 1 4 8 2 3 7 5 9
5 7 8 9 6 1 4 2 3
3 2 9 4 5 7 8 1 6
9 4 7 2 8 6 5 3 1
1 8 5 7 3 9 2 6 4
2 6 3 1 4 5 9 7 8
```

## <a name="pi--example"></a>PI (π)-voor beeld

In het voor beeld pi wordt een statistische (quasi-Monte Carlo)-methode gebruikt om de waarde van pi te schatten. Punten worden in wille keurige volg orde in een vier Kante eenheid geplaatst. Het kwadraat bevat ook een cirkel. De kans dat de punten binnen de cirkel vallen, is gelijk aan het gebied van de cirkel, Pi/4. De waarde van pi kan worden geschat op basis van de waarde van 4R. R is de verhouding van het aantal punten in de cirkel tot het totale aantal punten in het vier kant. Hoe groter het voor beeld van de gebruikte punten, hoe beter de schatting is.

Gebruik de volgende opdracht om dit voor beeld uit te voeren. Deze opdracht maakt gebruik van 16 kaarten met 10.000.000 voor beelden om de waarde van pi te schatten:

```bash
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000
```

De waarde die door deze opdracht wordt geretourneerd, is vergelijkbaar met **3.14159155000000000000**. Voor verwijzingen is de eerste tien decimale posities van Pi 3,1415926535.

## <a name="10-gb-graysort-example"></a>10 GB GraySort-voor beeld

GraySort is een bench Mark-Sorteer bewerking. De metriek is de sorteer frequentie (TB/minuut) die wordt behaald bij het sorteren van grote hoeveel heden gegevens, meestal een minimum van 100 TB.

In dit voor beeld wordt gebruikgemaakt van een bescheiden 10 GB aan gegevens, zodat deze relatief snel kunnen worden uitgevoerd. Het maakt gebruik van de MapReduce-toepassingen die zijn ontwikkeld door Owen O'Malley en Arun Murthy. Met deze toepassingen is het jaarlijkse algemene doel ("Daytona") terabyte-benchmark waarde in 2009 gewonnen, met een frequentie van 0,578 TB/min (100 TB in 173 minuten). Zie voor meer informatie over deze en andere sorterings benchmarks de site voor het [sorteren van benchmarks](https://sortbenchmark.org/) .

In dit voor beeld worden drie sets MapReduce-Program ma's gebruikt:

* **TeraGen**: een MapReduce-programma waarmee rijen met gegevens worden gegenereerd om te sorteren

* **TeraSort**: Hiermee worden de invoer gegevens gesampled en wordt MapReduce gebruikt om de gegevens in een totale volg orde te sorteren

    TeraSort is een standaard MapReduce-sortering, met uitzonde ring van een aangepaste partitioner. De partitioner gebruikt een gesorteerde lijst met N-1 beproefde sleutels waarmee het sleutel bereik voor elke reductie wordt gedefinieerd. In het bijzonder worden alle sleutels, zoals het voor beeld [i-1] <= Key < voor beeld [i], verzonden om i te verminderen. Deze partitioner garandeert dat de uitvoer van minder dan de uitvoer van i + 1 vermindert.

* **TeraValidate**: een MapReduce-programma dat valideert dat de uitvoer wereld wijd is gesorteerd

    Er wordt één kaart per bestand in de uitvoermap gemaakt en elke kaart zorgt ervoor dat elke sleutel kleiner dan of gelijk is aan de vorige. De functie map genereert records van de eerste en laatste sleutels van elk bestand. De functie verminderen zorgt ervoor dat de eerste sleutel van bestand i groter is dan de laatste sleutel van bestand i-1. Eventuele problemen worden gerapporteerd als uitvoer van de reductie fase, met de sleutels die niet in orde zijn.

Gebruik de volgende stappen om gegevens te genereren, te sorteren en de uitvoer te valideren:

1. 10 GB aan gegevens genereren, die worden opgeslagen in de standaard opslag van het HDInsight-cluster op `/example/data/10GB-sort-input` :

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input
    ```

    De `-Dmapred.map.tasks` vertelt Hadoop hoeveel toewijzings taken voor deze taak moeten worden gebruikt. De laatste twee para meters geven de taak de opdracht om 10 GB aan gegevens te maken en op te slaan in `/example/data/10GB-sort-input` .

2. Gebruik de volgende opdracht om de gegevens te sorteren:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output
    ```

    De `-Dmapred.reduce.tasks` vertelt Hadoop hoe veel minder taken voor de taak moeten worden gebruikt. De laatste twee para meters zijn alleen de invoer-en uitvoer locaties voor gegevens.

3. Gebruik de volgende informatie om de gegevens te valideren die door de sorteer bewerking worden gegenereerd:

    ```bash
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate
    ```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de voor beelden kunt uitvoeren die zijn opgenomen in de op Linux gebaseerde HDInsight-clusters. Zie de volgende onderwerpen voor zelf studies over het gebruik van Pig, Hive en MapReduce met HDInsight:

* [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md)
* [MapReduce gebruiken met Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)
