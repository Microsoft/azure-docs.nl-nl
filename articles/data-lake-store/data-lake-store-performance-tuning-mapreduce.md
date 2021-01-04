---
title: Azure Data Lake Storage Gen1 prestaties afstemmen-MapReduce
description: Meer informatie over het afstemmen van de prestaties van MapReduce in Azure Data Lake Storage Gen1, zoals para meters, richt lijnen, een voor beeld van de berekening en beperkingen.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 2549413241e422fb1e0e5e1f079c287e0b7cf005
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723725"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Richt lijnen voor het afstemmen van de prestaties voor MapReduce in HDInsight en Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Azure data Lake Storage gen1-account**. Zie [aan de slag met Azure data Lake Storage gen1](data-lake-store-get-started-portal.md) voor instructies over het maken van een account.
* **Azure HDInsight-cluster** met toegang tot een Data Lake Storage gen1-account. Zie [een HDInsight-cluster met data Lake Storage gen1 maken](data-lake-store-hdinsight-hadoop-use-portal.md). Zorg ervoor dat Extern bureaublad voor het cluster is ingeschakeld.
* **MapReduce gebruiken in HDInsight**. Zie [MapReduce gebruiken in Hadoop op HDInsight](../hdinsight/hadoop/hdinsight-use-mapreduce.md) voor meer informatie.
* **Bekijk de richt lijnen voor het afstemmen van de prestaties voor data Lake Storage gen1**. Zie [Data Lake Storage gen1 richt lijnen voor het afstemmen van prestaties](./data-lake-store-performance-tuning-guidance.md) voor algemene concepten.

## <a name="parameters"></a>Parameters

Wanneer u MapReduce-taken uitvoert, zijn dit de belangrijkste para meters die u kunt configureren om de prestaties van Data Lake Storage Gen1 te verbeteren:

|Parameter      | Beschrijving  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  De hoeveelheid geheugen die aan elke Mapper moet worden toegewezen.  |
|`Mapreduce.job.maps`     |  Het aantal toewijzings taken per taak.  |
|`Mapreduce.reduce.memory.mb`     |  De hoeveelheid geheugen die aan elke reductier moet worden toegewezen.  |
|`Mapreduce.job.reduces`    |   Het aantal verlaagde taken per taak.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>MapReduce. map. Memory/MapReduce. Verminder. Memory

Pas dit aantal aan op basis van de hoeveelheid geheugen die nodig is voor de kaart en/of de taak verminderen. U kunt de standaard waarden van `mapreduce.map.memory` en `mapreduce.reduce.memory` in Ambari weer geven via de garen configuratie. Ga in Ambari naar GARENs en Bekijk het tabblad **configuratie** . Het garen geheugen wordt weer gegeven.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>MapReduce. job. Mapss/MapReduce. job. reduceert

Hiermee bepaalt u het maximum aantal mappers of verminderers dat moet worden gemaakt. Het aantal splitsingen bepaalt hoeveel toewijzingen voor de MapReduce-taak worden gemaakt. Daarom kunt u minder mappers aanvragen dan u hebt aangevraagd als er minder splitsingen zijn dan het aangevraagde aantal mappers.

## <a name="guidance"></a>Hulp

### <a name="step-1-determine-number-of-jobs-running"></a>Stap 1: het aantal uitgevoerde taken bepalen

MapReduce maakt standaard gebruik van het hele cluster voor uw taak. U kunt minder van het cluster gebruiken door minder mappers te gebruiken dan er beschik bare containers zijn. In de instructies in dit document wordt ervan uitgegaan dat uw toepassing de enige toepassing is die in uw cluster wordt uitgevoerd.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Stap 2: Stel MapReduce. kaart. Memory/MapReduce. Reducing. memory in

De grootte van het geheugen voor het toewijzen en verminderen van taken is afhankelijk van uw specifieke taak. U kunt de geheugen grootte verminderen als u gelijktijdigheid wilt verg Roten. Het aantal gelijktijdig uitgevoerde taken is afhankelijk van het aantal containers. Door de hoeveelheid geheugen per Mapper of extra verlaging te verlagen, kunnen er meer containers worden gemaakt, waarmee meer toewijzingen of verminderers gelijktijdig worden uitgevoerd. Het verminderen van de hoeveelheid geheugen die te groot is, kan ertoe leiden dat er onvoldoende geheugen beschikbaar is voor sommige processen. Als er een heap-fout optreedt tijdens het uitvoeren van uw taak, verg root u het geheugen per Mapper of minder. Houd er rekening mee dat het toevoegen van meer containers extra overhead voor elke extra container toevoegt, waardoor de prestaties mogelijk nadelig kunnen afnemen. Een andere mogelijkheid is om meer geheugen te verkrijgen door gebruik te maken van een cluster met een grotere hoeveelheid geheugen of het aantal knoop punten in het cluster te verhogen. Meer geheugens worden gebruikt om meer containers te gebruiken. Dit betekent dat u meer dan gelijktijdig kunt instellen.

### <a name="step-3-determine-total-yarn-memory"></a>Stap 3: de totale hoeveelheid garen bepalen

Als u MapReduce. job. Mapss/MapReduce. job. reduceert, moet u rekening houden met de totale hoeveelheid garen geheugen die beschikbaar is voor gebruik. Deze informatie is beschikbaar in Ambari. Navigeer naar GARENs en Bekijk het tabblad **configuratie** . Het garen geheugen wordt in dit venster weer gegeven. Vermenigvuldig het garen geheugen met het aantal knoop punten in het cluster om het totale garen geheugen te verkrijgen.

`Total YARN memory = nodes * YARN memory per node`

Als u een leeg cluster gebruikt, kan het geheugen de totale hoeveelheid garen van het cluster zijn. Als andere toepassingen geheugen gebruiken, kunt u ervoor kiezen om alleen een gedeelte van het geheugen van uw cluster te gebruiken door het aantal mappers of verminderers te verminderen tot het aantal containers dat u wilt gebruiken.

### <a name="step-4-calculate-number-of-yarn-containers"></a>Stap 4: het aantal GARENs

GAREN-containers bepalen de hoeveelheid gelijktijdige Beschik baarheid voor de taak. Profiteer van het totale aantal garen geheugen en splits dit door MapReduce. map. Memory.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Stap 5: Stel MapReduce. job. Mapss/MapReduce. job. reduceert

Stel MapReduce. job. Mapss/MapReduce. job. reduceert ten minste het aantal beschik bare containers. U kunt verder experimenteren door het aantal mappers en verminderers te verhogen om te zien of u betere prestaties krijgt. Houd er rekening mee dat meer toewijzingen meer overhead hebben, zodat er te veel mappers zijn die de prestaties kunnen verminderen.

CPU-planning en CPU-isolatie zijn standaard uitgeschakeld zodat het aantal garen containers wordt beperkt door het geheugen.

## <a name="example-calculation"></a>Voorbeeld berekening

Stel dat u momenteel een cluster hebt dat bestaat uit 8 D14-knoop punten en u een I/O-intensieve taak wilt uitvoeren. Dit zijn de berekeningen die u moet uitvoeren:

### <a name="step-1-determine-number-of-jobs-running"></a>Stap 1: het aantal uitgevoerde taken bepalen

In ons voor beeld gaan we ervan uit dat de enige actieve taak is.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Stap 2: Stel MapReduce. kaart. Memory/MapReduce. Reducing. memory in

In ons voor beeld voert u een I/O-intensieve taak uit en beslist u dat 3 GB geheugen voor de toewijzing van taken voldoende is.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>Stap 3: de totale hoeveelheid garen bepalen

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>Stap 4: aantal GARENs

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Stap 5: Stel MapReduce. job. Mapss/MapReduce. job. reduceert

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Beperkingen

**Data Lake Storage Gen1 beperking**

Als multi tenant-service, Data Lake Storage Gen1 instellen bandbreedte limieten op account niveau. Als u deze limieten bereikt, worden er taak fouten weer geven. Dit kan worden geïdentificeerd door beperkings fouten in taak logboeken te observeren. Als u meer band breedte voor uw taak nodig hebt, kunt u contact met ons opnemen.

Als u wilt controleren of u een beperking krijgt, moet u de logboek registratie voor fout opsporing inschakelen op de client. U kunt dit als volgt doen:

1. Plaats de volgende eigenschap in de log4j-eigenschappen in Ambari > GARENs > config > Advanced garens-log4j: log4j. logger. com. Microsoft. Azure. datalake. Store = DEBUG

2. Start alle knoop punten/service opnieuw op om de configuratie van kracht te laten worden.

3. Als u een beperking krijgt, ziet u de fout code HTTP 429 in het garen logboek bestand. Het garen logboek bestand bevindt zich in/tmp/ &lt; User &gt; /Yarn.log

## <a name="examples-to-run"></a>Voor beelden om uit te voeren

Om te laten zien hoe MapReduce wordt uitgevoerd op Data Lake Storage Gen1, ziet u hier een voor beeld van een code die op een cluster werd uitgevoerd met de volgende instellingen:

* D14v2 van 16 knoop punten
* Hadoop-cluster met HDI 3,6

Hier volgen enkele voorbeeld opdrachten voor het uitvoeren van MapReduce Teragen, Terasort en Teravalidate. U kunt deze opdrachten aanpassen op basis van uw resources.

### <a name="teragen"></a>Teragen

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidate

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```