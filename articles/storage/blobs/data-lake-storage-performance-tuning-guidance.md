---
title: Azure Data Lake Storage Gen2 optimaliseren voor prestaties | Microsoft Docs
description: Meer informatie over het optimaliseren van Azure Data Lake Storage Gen2 voor prestaties. Gegevens opnemen, uw gegevensset structureren en nog veel meer.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 834672274ade1f8551e86e7c636c4625368d997c
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652191"
---
# <a name="optimize-azure-data-lake-storage-gen2-for-performance"></a>Azure Data Lake Storage Gen2 optimaliseren voor prestaties

Azure Data Lake Storage Gen2 ondersteunt hoge door Voer voor I/O-intensieve analyses en gegevens verplaatsing.  In Data Lake Storage Gen2, met behulp van alle beschik bare door Voer – de hoeveelheid gegevens die per seconde kan worden gelezen of geschreven, is belang rijk om de beste prestaties te verkrijgen.  Dit wordt bereikt door zo veel mogelijk Lees-en schrijf bewerkingen parallel uit te voeren.

![Prestaties Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen2 kan worden geschaald om de benodigde door Voer voor alle analyse scenario's te bieden. Een Data Lake Storage Gen2-account biedt standaard automatisch voldoende door Voer om te voldoen aan de behoeften van een brede categorie van use cases. Voor de gevallen waarin klanten de standaard limiet uitvoeren, kan het Data Lake Storage Gen2-account worden geconfigureerd om meer door voer te bieden door contact op te nemen met de [ondersteuning van Azure](https://azure.microsoft.com/support/faq/).

## <a name="data-ingestion"></a>Gegevensopname

Bij het opnemen van gegevens van een bron systeem naar Data Lake Storage Gen2, is het belang rijk om te overwegen dat de bron-hardware, de bron netwerkhardware en de netwerk verbinding met Data Lake Storage Gen2 de knel punt kunnen zijn.  

![Diagram met de factoren waarmee rekening moet worden gehouden bij het opnemen van gegevens van een bron systeem naar Data Lake Storage Gen2.](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

Het is belang rijk om ervoor te zorgen dat de verplaatsing van gegevens niet wordt beïnvloed door deze factoren.

### <a name="source-hardware"></a>Bron-hardware

Of u nu gebruikmaakt van on-premises machines of Vm's in azure, moet u zorgvuldig de juiste hardware selecteren. Voor de hardware van de bron schijf krijgt u de voor keur aan Hdd's en Ssd's met snellere spindles. Gebruik de snelste Nic's voor de hardware van het bron netwerk.  In azure wordt u aangeraden Azure D14 Vm's te hebben die de juiste krachtige hardware voor schijf-en netwerk apparatuur hebben.

### <a name="network-connectivity-to-data-lake-storage-gen2"></a>De netwerk verbinding met de Data Lake Storage Gen2

De netwerk verbinding tussen de bron gegevens en Data Lake Storage Gen2 kan soms het knel punt zijn. Als uw bron gegevens on-premises zijn, kunt u een speciale koppeling met [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) gebruiken. Als uw bron gegevens zich in azure bevinden, zijn de prestaties het beste wanneer de gegevens zich in dezelfde Azure-regio bevinden als het Data Lake Storage Gen2-account.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Hulp middelen voor gegevens opname configureren voor maximale parallel Lise ring

Als u de bovenstaande knel punten voor de bron-en netwerk connectiviteit hebt opgelost, kunt u de opname hulpprogramma's configureren. De volgende tabel bevat een overzicht van de belangrijkste instellingen voor verschillende veelgebruikte hulpprogram ma's voor opname en biedt gedetailleerde artikelen voor het afstemmen van de prestaties.  Ga naar dit [artikel](data-lake-storage-data-scenarios.md)voor meer informatie over welk hulp programma u moet gebruiken voor uw scenario.

| Hulpprogramma               | Instellingen | Meer Details                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| DistCp            | -m (Mapper)   | [Koppeling](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Koppeling](../../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS. Azure. Block. size,-m (Mapper)    |   [Koppeling](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)        |

## <a name="structure-your-data-set"></a>De structuur van uw gegevensset instellen

Wanneer gegevens worden opgeslagen in Data Lake Storage Gen2, hebben de bestands grootte, het aantal bestanden en de mapstructuur een invloed op de prestaties.  In de volgende sectie worden aanbevolen procedures op deze gebieden beschreven.  

### <a name="file-size"></a>Bestandsgrootte

Normaal gesp roken hebben analyse-engines als HDInsight en Azure Data Lake Analytics een overhead per bestand. Als u uw gegevens als veel kleine bestanden opslaat, kan dit een negatieve invloed hebben op de prestaties. In het algemeen kunt u uw gegevens indelen in bestanden met een grotere grootte voor betere prestaties (256 MB tot 100 GB). Sommige engines en toepassingen hebben mogelijk problemen met het efficiënt verwerken van bestanden die groter zijn dan 100 GB.

Soms hebben gegevens pijplijnen een beperkte controle over de onbewerkte gegevens die veel kleine bestanden hebben. Over het algemeen raden wij aan dat uw systeem een bepaalde Sorteer procedure heeft voor het samen voegen van kleine bestanden tot grotere records voor gebruik door downstream-toepassingen.

### <a name="organizing-time-series-data-in-folders"></a>Time Series-gegevens in mappen ordenen

Voor Hive-workloads kan het maken van een partitie van Time Series-gegevens helpen sommige query's alleen een subset van de gegevens te lezen die de prestaties verbeteren.    

Deze pijp lijnen die gegevens van tijd reeksen opnemen, plaatsen hun bestanden vaak met een zeer gestructureerde naam voor bestanden en mappen. Hieronder ziet u een algemeen voor beeld van gegevens die zijn gestructureerd op datum:

*\DataSet\YYYY\MM\DD\ datafile_YYYY_MM_DD. TSV*

U ziet dat de datetime-informatie zowel als mappen als in de bestands naam wordt weer gegeven.

Voor datum en tijd is het volgende een algemeen patroon

*\DataSet\YYYY\MM\DD\HH\mm\ datafile_YYYY_MM_DD_HH_mm. TSV*

Daarnaast moet de keuze die u maakt met de map en de bestands organisatie worden geoptimaliseerd voor de grotere bestands grootten en een redelijk aantal bestanden in elke map.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Het optimaliseren van I/O-taken op Hadoop-en Spark-workloads in HDInsight

Taken vallen in een van de volgende drie categorieën:

* **CPU-intensief.**  Deze taken hebben een lange reken tijd met een minimale I/O-tijd.  Voor beelden zijn machine learning en verwerkings taken in natuurlijke taal.  
* **Geheugenintensieve.**  Deze taken gebruiken veel geheugen.  Voor beelden zijn PageRank en real-time analyse taken.  
* **I/O-intensief.**  Deze taken best Eden de meeste tijd bij het uitvoeren van I/O.  Een veelvoorkomend voor beeld is een Kopieer taak die alleen lees-en schrijf bewerkingen uitvoert.  Andere voor beelden zijn gegevens voorbereidings taken waarmee veel gegevens worden gelezen, gegevens transformatie wordt uitgevoerd en de gegevens vervolgens worden teruggeschreven naar de Store.  

De volgende richt lijnen zijn alleen van toepassing op I/O-intensieve taken.

## <a name="general-considerations"></a>Algemene overwegingen

U kunt een taak hebben die zoveel 100 MB per bewerking leest of schrijft, maar een buffer van die grootte kan de prestaties nadelig beïnvloeden.
U kunt de prestaties optimaliseren door de grootte van een I/O-bewerking tussen 4 MB en 16 MB te gebruiken.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Algemene overwegingen voor een HDInsight-cluster

* **HDInsight-versies.** Gebruik de meest recente versie van HDInsight voor de beste prestaties.
* **Secties.** Plaats het Data Lake Storage Gen2-account in dezelfde regio als het HDInsight-cluster.  

An HDInsight cluster bestaat uit twee hoofd knooppunten en een aantal worker-knoop punten. Elk worker-knoop punt biedt een specifiek aantal kernen en geheugen, dat wordt bepaald door het VM-type.  Bij het uitvoeren van een taak is garen de resource-onderhandelaar die het beschik bare geheugen en de kernen toewijst om containers te maken.  Elke container voert de taken uit die nodig zijn om de taak te volt ooien.  Containers worden parallel uitgevoerd om taken snel te verwerken. De prestaties zijn daarom verbeterd door zo veel mogelijk parallelle containers uit te voeren.

Er zijn drie lagen in een HDInsight-cluster die kunnen worden afgestemd om het aantal containers te verhogen en alle beschik bare door voer te gebruiken.  

* **Fysieke laag**
* **Laag met garen**
* **Workload**

### <a name="physical-layer"></a>Fysieke laag

**Voer het cluster uit met meer knoop punten en/of grotere Vm's.**  Een groter cluster biedt u de mogelijkheid om meer GARENs-containers uit te voeren, zoals wordt weer gegeven in de onderstaande afbeelding.

![Diagram dat laat zien hoe een groter cluster u in staat stelt om meer GARENs uit te voeren.](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Gebruik Vm's met meer netwerk bandbreedte.**  De hoeveelheid netwerk bandbreedte kan een knel punt zijn als er minder netwerk bandbreedte is dan Data Lake Storage Gen2 door voer.  Verschillende Vm's hebben een verschillende grootte voor de netwerk bandbreedte.  Kies een VM-type met de grootst mogelijke netwerk bandbreedte.

### <a name="yarn-layer"></a>Laag met garen

**Gebruik kleinere garen-containers.**  Verklein de grootte van elke garen container om meer containers te maken met dezelfde hoeveelheid resources.

![Diagram waarin het resultaat wordt weer gegeven wanneer u de grootte van elke garen container reduceert om meer containers te maken.](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

Afhankelijk van uw werk belasting, wordt er altijd een minimale grootte van een garen container vereist. Als u te klein een container kiest, worden uw taken uitgevoerd in plaats van geheugen. Een garen mag doorgaans niet kleiner zijn dan 1 GB. Het is gebruikelijk om 3 GB-GARENs te zien. Voor sommige workloads hebt u mogelijk grotere GARENs nodig.  

**Verhoog de kernen per garen container.**  Verhoog het aantal kern geheugens dat aan elke container is toegewezen om het aantal parallelle taken dat in elke container wordt uitgevoerd, te verg Roten.  Dit werkt voor toepassingen zoals Spark waarmee meerdere taken per container worden uitgevoerd.  Voor toepassingen als-componenten die één thread in elke container uitvoeren, is het beter om meer containers te hebben in plaats van meer kernen per container.

### <a name="workload-layer"></a>Workload

**Alle beschik bare containers gebruiken.**  Stel in hoeveel taken gelijk of groter dan het aantal beschik bare containers moeten zijn, zodat alle resources worden gebruikt.

![Diagram waarin het gebruik van alle containers wordt weer gegeven.](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**Mislukte taken zijn kostbaar.** Als elke taak een grote hoeveelheid gegevens kan verwerken, resulteert het mislukken van een taak in een dure nieuwe poging.  Daarom is het beter om meer taken te maken, die elk een kleine hoeveelheid gegevens verwerken.

Naast de bovenstaande algemene richt lijnen heeft elke toepassing verschillende para meters die beschikbaar zijn voor het afstemmen van die specifieke toepassing. In de volgende tabel ziet u een aantal para meters en koppelingen om aan de slag te gaan met het afstemmen van de prestaties van elke toepassing.

| Workload | Para meter voor het instellen van taken |
|----------|------------------------|
| [Spark in HDInsight](data-lake-storage-performance-tuning-spark.md) | <ul><li>Num-uitvoerende modules</li><li>Uitvoerder-geheugen</li><li>Uitvoerder-kernen</li></ul> |
| [Hive in HDInsight](data-lake-storage-performance-tuning-hive.md) | <ul><li>component. TEZ. container. size</li></ul> |
| [MapReduce in HDInsight](data-lake-storage-performance-tuning-mapreduce.md) | <ul><li>MapReduce. map. Memory</li><li>MapReduce. job. Maps</li><li>MapReduce. Reduc. Memory</li><li>MapReduce. job. reduceert</li></ul> |
| [Storm op HDInsight](data-lake-storage-performance-tuning-storm.md)| <ul><li>Aantal werk processen</li><li>Aantal exemplaren van de Spout-uitvoerder</li><li>Aantal exemplaren van de bout-uitvoerder </li><li>Aantal Spout-taken</li><li>Aantal Schicht-taken</li></ul>|

## <a name="see-also"></a>Zie ook
* [Overzicht van Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)