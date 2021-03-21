---
title: Azure Data Lake Storage Gen1 prestaties afstemmen
description: Meer informatie over het gebruik van alle beschik bare door Voer in Azure Data Lake Storage Gen1 is belang rijk om de beste prestaties te verkrijgen door zo veel mogelijk Lees-en schrijf bewerkingen tegelijk uit te voeren.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: twooley
ms.openlocfilehash: c7f16dd9ea450185893164e10928c7022d6ab5a6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97724677"
---
# <a name="tune-azure-data-lake-storage-gen1-for-performance"></a>Azure Data Lake Storage Gen1 voor prestaties afstemmen

Data Lake Storage Gen1 ondersteunt hoge door Voer voor I/O-intensieve analyses en gegevens verplaatsing. In Data Lake Storage Gen1, met behulp van alle beschik bare door Voer – de hoeveelheid gegevens die per seconde kan worden gelezen of geschreven, is belang rijk om de beste prestaties te verkrijgen. Dit wordt bereikt door zo veel mogelijk Lees-en schrijf bewerkingen parallel uit te voeren.

![Prestaties Data Lake Storage Gen1](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen1 kan worden geschaald om de benodigde door Voer voor alle analyse scenario's te bieden. Een Data Lake Storage Gen1-account biedt standaard automatisch voldoende door Voer om te voldoen aan de behoeften van een brede categorie van use cases. Voor de gevallen waarin klanten de standaard limiet uitvoeren, kan het Data Lake Storage Gen1-account worden geconfigureerd om meer door voer te bieden door contact op te nemen met micro soft ondersteuning.

## <a name="data-ingestion"></a>Gegevensopname

Bij het opnemen van gegevens van een bron systeem naar Data Lake Storage Gen1, is het belang rijk om te overwegen dat de bron-hardware, de bron netwerkhardware en de netwerk verbinding met Data Lake Storage Gen1 de knel punt kunnen zijn.

![Diagram dat laat zien dat de bron-hardware, de bron netwerkhardware en de netwerk verbinding met Data Lake Storage Gen1 het knel punt kunnen zijn.](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

Het is belang rijk om ervoor te zorgen dat de verplaatsing van gegevens niet wordt beïnvloed door deze factoren.

### <a name="source-hardware"></a>Bron-hardware

Of u nu gebruikmaakt van on-premises machines of Vm's in azure, moet u zorgvuldig de juiste hardware selecteren. Voor de hardware van de bron schijf krijgt u de voor keur aan Hdd's en Ssd's met snellere spindles. Gebruik de snelste Nic's voor de hardware van het bron netwerk. In azure wordt geadviseerd Azure D14 Vm's met de juiste krachtige schijf-en netwerkhardware.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>De netwerk verbinding met de Data Lake Storage Gen1

De netwerk verbinding tussen de bron gegevens en Data Lake Storage Gen1 kan soms het knel punt zijn. Als uw bron gegevens on-premises zijn, kunt u een speciale koppeling met [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) gebruiken. Als uw bron gegevens zich in azure bevinden, zijn de prestaties het beste wanneer de gegevens zich in dezelfde Azure-regio bevinden als het Data Lake Storage Gen1-account.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Hulp middelen voor gegevens opname configureren voor maximale parallel Lise ring

Nadat u de bron-en netwerk connectiviteit knelpunten hebt opgelost, bent u klaar om uw opname hulpprogramma's te configureren. De volgende tabel bevat een overzicht van de belangrijkste instellingen voor verschillende veelgebruikte hulpprogram ma's voor opname en biedt gedetailleerde artikelen voor het afstemmen van de prestaties. Ga naar dit [artikel](./data-lake-store-data-scenarios.md)voor meer informatie over welk hulp programma u moet gebruiken voor uw scenario.

| Hulpprogramma          | Instellingen | Meer Details                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount | [Koppeling](./data-lake-store-get-started-powershell.md) |
| AdlCopy    | Azure Data Lake Analytics eenheden | [Koppeling](./data-lake-store-copy-data-azure-storage-blob.md#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (Mapper) | [Koppeling](./data-lake-store-copy-data-wasb-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies | [Koppeling](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS. Azure. Block. size,-m (Mapper) | [Koppeling](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)        |

## <a name="structure-your-data-set"></a>De structuur van uw gegevensset instellen

Wanneer gegevens worden opgeslagen in Data Lake Storage Gen1, zijn de bestands grootte, het aantal bestanden en de mapstructuur van invloed op de prestaties. In de volgende sectie worden aanbevolen procedures op deze gebieden beschreven.

### <a name="file-size"></a>Bestandsgrootte

Normaal gesp roken hebben analyse-engines als HDInsight en Azure Data Lake Analytics een overhead per bestand. Als u uw gegevens als veel kleine bestanden opslaat, kan dit een negatieve invloed hebben op de prestaties.

In het algemeen kunt u uw gegevens indelen in bestanden met een grotere grootte voor betere prestaties. Als vuist regel organiseert u gegevens sets in bestanden van 256 MB of groter. In sommige gevallen, zoals afbeeldingen en binaire gegevens, is het niet mogelijk deze parallel te verwerken. In deze gevallen is het raadzaam om afzonderlijke bestanden onder 2 GB te blijven gebruiken.

Soms hebben gegevens pijplijnen een beperkte controle over de onbewerkte gegevens die veel kleine bestanden hebben. Het is raadzaam om een ' koken ' proces te hebben dat grotere bestanden genereert die worden gebruikt voor downstream-toepassingen.

### <a name="organize-time-series-data-in-folders"></a>Gegevens in de tijd reeks ordenen in mappen

Voor Hive-en ADLA-werk belastingen kan het maken van een partitie van Time Series-gegevens helpen sommige query's alleen een subset van de gegevens lezen, waardoor de prestaties worden verbeterd.

Deze pijp lijnen die gegevens van tijd reeksen opnemen, plaatsen hun bestanden vaak met een gestructureerde naam voor bestanden en mappen. Hier volgt een voor beeld van een algemeen overzicht van gegevens die zijn gestructureerd op datum: *\dataset\yyyy\mm\dd\ datafile_YYYY_MM_DD. TSV*.

U ziet dat de datetime-informatie zowel als mappen als in de bestands naam wordt weer gegeven.

Voor datum en tijd is het volgende een algemeen patroon: *\dataset\yyyy\mm\dd\hh\mm\ datafile_YYYY_MM_DD_HH_mm. TSV*.

Daarnaast moet de keuze die u maakt met de map en de bestands organisatie worden geoptimaliseerd voor de grotere bestands grootten en een redelijk aantal bestanden in elke map.

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optimaliseer I/O-taken op Hadoop-en Spark-workloads in HDInsight

Taken vallen in een van de volgende drie categorieën:

* **CPU-intensief.** Deze taken hebben een lange reken tijd met een minimale I/O-tijd. Voor beelden zijn machine learning en verwerkings taken in natuurlijke taal.
* **Geheugenintensieve.** Deze taken gebruiken veel geheugen. Voor beelden zijn PageRank en real-time analyse taken.
* **I/O-intensief.** Deze taken best Eden de meeste tijd bij het uitvoeren van I/O. Een veelvoorkomend voor beeld is een Kopieer taak die alleen lees-en schrijf bewerkingen uitvoert. Andere voor beelden zijn gegevens voorbereidings taken waarmee talloze gegevens worden gelezen, wat gegevens transformatie kan worden uitgevoerd en de gegevens vervolgens terug naar de Store worden geschreven.

De volgende richt lijnen zijn alleen van toepassing op I/O-intensieve taken.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Algemene overwegingen voor een HDInsight-cluster

* **HDInsight-versies.** Gebruik de meest recente versie van HDInsight voor de beste prestaties.
* **Secties.** Plaats het Data Lake Storage Gen1-account in dezelfde regio als het HDInsight-cluster.

An HDInsight cluster bestaat uit twee hoofd knooppunten en een aantal worker-knoop punten. Elk worker-knoop punt biedt een specifiek aantal kernen en geheugen, dat wordt bepaald door het VM-type. Bij het uitvoeren van een taak is garen de resource-onderhandelaar die het beschik bare geheugen en de kernen toewijst om containers te maken. Elke container voert de taken uit die nodig zijn om de taak te volt ooien. Containers worden parallel uitgevoerd om taken snel te verwerken. De prestaties zijn daarom verbeterd door zo veel mogelijk parallelle containers uit te voeren.

Er zijn drie lagen in een HDInsight-cluster die kunnen worden afgestemd om het aantal containers te verhogen en alle beschik bare door voer te gebruiken.

* **Fysieke laag**
* **Laag met garen**
* **Workload**

### <a name="physical-layer"></a>Fysieke laag

**Voer het cluster uit met meer knoop punten en/of grotere Vm's.** Een groter cluster biedt u de mogelijkheid om meer GARENs-containers uit te voeren, zoals wordt weer gegeven in de onderstaande afbeelding.

![Diagram waarin het gebruik van meer GARENs in de andere houders wordt weer gegeven.](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Gebruik Vm's met meer netwerk bandbreedte.** De hoeveelheid netwerk bandbreedte kan een knel punt zijn als er minder netwerk bandbreedte is dan Data Lake Storage Gen1 door voer. Verschillende Vm's hebben een verschillende grootte voor de netwerk bandbreedte. Kies een VM-type met de grootst mogelijke netwerk bandbreedte.

### <a name="yarn-layer"></a>Laag met garen

**Gebruik kleinere garen-containers.** Verklein de grootte van elke garen container om meer containers te maken met dezelfde hoeveelheid resources.

![Diagram waarin het gebruik van kleinere garen containers wordt weer gegeven.](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

Afhankelijk van uw werk belasting, wordt er altijd een minimale grootte van een garen container vereist. Als u te klein een container kiest, worden uw taken uitgevoerd in plaats van geheugen. Een garen mag doorgaans niet kleiner zijn dan 1 GB. Het is gebruikelijk om 3 GB GARENs-containers te zien. Voor sommige workloads hebt u mogelijk grotere GARENs nodig.

**Verhoog de kernen per garen container.** Verhoog het aantal kern geheugens dat aan elke container is toegewezen om het aantal parallelle taken dat in elke container wordt uitgevoerd, te verg Roten. Dit werkt voor toepassingen als Spark, waarmee meerdere taken per container worden uitgevoerd. Voor toepassingen als-componenten waarop één thread in elke container wordt uitgevoerd, is het beter om meer containers te hebben in plaats van meer kernen per container.

### <a name="workload-layer"></a>Workload

**Alle beschik bare containers gebruiken.** Stel in hoeveel taken gelijk of groter dan het aantal beschik bare containers moeten zijn, zodat alle resources worden gebruikt.

![Diagram waarin het gebruik van alle beschik bare containers wordt weer gegeven.](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**Mislukte taken zijn kostbaar.** Als elke taak een grote hoeveelheid gegevens kan verwerken, resulteert het mislukken van een taak in een dure nieuwe poging. Daarom is het beter om meer taken te maken, die elk een kleine hoeveelheid gegevens verwerken.

Naast de bovenstaande algemene richt lijnen heeft elke toepassing verschillende para meters die beschikbaar zijn voor het afstemmen van die specifieke toepassing. In de volgende tabel ziet u een aantal para meters en koppelingen om aan de slag te gaan met het afstemmen van de prestaties van elke toepassing.

| Workload               | Para meter voor het instellen van taken                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Spark in HDInsight](data-lake-store-performance-tuning-spark.md)  | <ul><li>Num-uitvoerende modules</li><li>Uitvoerder-geheugen</li><li>Uitvoerder-kernen</li></ul> |
| [Hive in HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>component. TEZ. container. size</li></ul>         |
| [MapReduce in HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>MapReduce. map. Memory</li><li>MapReduce. job. Maps</li><li>MapReduce. Reduc. Memory</li><li>MapReduce. job. reduceert</li></ul> |
| [Storm op HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Aantal werk processen</li><li>Aantal exemplaren van de Spout-uitvoerder</li><li>Aantal exemplaren van de bout-uitvoerder </li><li>Aantal Spout-taken</li><li>Aantal Schicht-taken</li></ul>|

## <a name="see-also"></a>Zie ook

* [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Aan de slag met Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)