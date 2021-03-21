---
title: 'Prestaties afstemmen: MapReduce, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Meer informatie over richt lijnen voor het afstemmen van de prestaties van de kaart en het verminderen van taken op Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b95d37e1725940799750dbd3c29174d9855390d6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95912923"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>Prestaties afstemmen: MapReduce, HDInsight & Azure Data Lake Storage Gen2

Begrijp de factoren waarmee u rekening moet houden bij het afstemmen van de prestaties van de kaart verminderen van de taken. In dit artikel wordt een scala aan richt lijnen voor het afstemmen van prestaties besproken.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Azure data Lake Storage Gen2-account**. Zie [Quick Start: een Azure data Lake Storage Gen2 Storage-account maken](../common/storage-account-create.md)voor instructies over het maken van een.
* **Azure HDInsight-cluster** met toegang tot een Data Lake Storage Gen2-account. Zie [Azure data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)
* **MapReduce gebruiken in HDInsight**.  Zie [MapReduce gebruiken in Hadoop op HDInsight](../../hdinsight/hadoop/hdinsight-use-mapreduce.md) voor meer informatie.
* **Richt lijnen voor het afstemmen van de prestaties van data Lake Storage Gen2**.  Zie [Data Lake Storage Gen2 richt lijnen voor het afstemmen van prestaties](data-lake-storage-performance-tuning-guidance.md) voor algemene concepten.

## <a name="parameters"></a>Parameters

Wanneer u MapReduce-taken uitvoert, zijn dit de para meters die u kunt configureren om de prestaties van Data Lake Storage Gen2 te verbeteren:

* **MapReduce. map. Memory. MB** : de hoeveelheid geheugen die aan elke Mapper moet worden toegewezen
* **MapReduce. job. Maps** : het aantal toewijzings taken per taak
* **MapReduce. Reducing. Memory. MB** : de hoeveelheid geheugen die aan elke reductier moet worden toegewezen
* **MapReduce. job. reduces** : het aantal minder taken per taak

**MapReduce. map. Memory/MapReduce. Verminder. Memory** Dit aantal moet worden aangepast op basis van de hoeveelheid geheugen die nodig is voor de toewijzing en/of het verminderen van de taak.  De standaard waarden van MapReduce. map. Memory en MapReduce. Reduc. Memory kunnen worden weer gegeven in Ambari via de garen configuratie.  Ga in Ambari naar GARENs en Bekijk het tabblad Configuratie.  Het garen geheugen wordt weer gegeven.  

**MapReduce. job. Mapss/MapReduce. job. reduceert** Hiermee bepaalt u het maximum aantal mappers of verminderers dat moet worden gemaakt.  Het aantal splitsingen bepaalt het aantal mappers dat voor de MapReduce-taak wordt gemaakt.  Daarom kunt u minder mappers ontvangen dan u hebt gevraagd als er minder splitsingen zijn dan het aangevraagde aantal mappers.       

## <a name="guidance"></a>Hulp

> [!NOTE]
> In de instructies in dit document wordt ervan uitgegaan dat uw toepassing de enige toepassing is die in uw cluster wordt uitgevoerd.

**Stap 1: het aantal uitgevoerde taken bepalen**

MapReduce maakt standaard gebruik van het hele cluster voor uw taak.  U kunt minder van het cluster gebruiken door minder mappers te gebruiken dan er beschik bare containers zijn.        

**Stap 2: Stel MapReduce. kaart. Memory/MapReduce. Reducing. memory in**

De grootte van het geheugen voor het toewijzen en verminderen van taken is afhankelijk van uw specifieke taak.  U kunt de geheugen grootte verminderen als u gelijktijdigheid wilt verg Roten.  Het aantal gelijktijdig uitgevoerde taken is afhankelijk van het aantal containers.  Door de hoeveelheid geheugen per Mapper of extra verlaging te verlagen, kunnen er meer containers worden gemaakt, waarmee meer toewijzingen of verminderers gelijktijdig worden uitgevoerd.  Het verminderen van de hoeveelheid geheugen die te groot is, kan ertoe leiden dat er onvoldoende geheugen beschikbaar is voor sommige processen.  Als er een heap-fout optreedt tijdens het uitvoeren van uw taak, moet u het geheugen per Mapper of minder doen toenemen.  Houd er rekening mee dat het toevoegen van meer containers extra overhead voor elke extra container toevoegt, waardoor de prestaties mogelijk kunnen afnemen.  Een andere mogelijkheid is om meer geheugen te verkrijgen door gebruik te maken van een cluster met een grotere hoeveelheid geheugen of het aantal knoop punten in het cluster te verhogen.  Meer geheugens worden gebruikt om meer containers te gebruiken. Dit betekent dat u meer dan gelijktijdig kunt instellen.  

**Stap 3: de totale hoeveelheid garen bepalen**

Als u MapReduce. job. Mapss/MapReduce. job. Reducing wilt afstemmen, moet u rekening houden met de totale hoeveelheid garen geheugen die beschikbaar is voor gebruik.  Deze informatie is beschikbaar in Ambari.  Navigeer naar GARENs en Bekijk het tabblad Configuratie.  Het garen geheugen wordt in dit venster weer gegeven.  U moet het garen geheugen vermenigvuldigen met het aantal knoop punten in het cluster om het totale garen geheugen te verkrijgen.

Totaal aantal GARENs = knoop punten * garen geheugen per knoop punt

Als u een leeg cluster gebruikt, kan het geheugen de totale hoeveelheid garen van het cluster zijn.  Als andere toepassingen geheugen gebruiken, kunt u ervoor kiezen om alleen een gedeelte van het geheugen van uw cluster te gebruiken door het aantal mappers of verminderers te verminderen tot het aantal containers dat u wilt gebruiken.  

**Stap 4: het aantal GARENs**

GAREN-containers bepalen de hoeveelheid gelijktijdige Beschik baarheid voor de taak.  Profiteer van het totale aantal garen geheugen en splits dit door MapReduce. map. Memory.  

\# van GARENs = totaal aantal GARENs Memory/MapReduce. kaart. Memory

**Stap 5: Stel MapReduce. job. Mapss/MapReduce. job. reduceert**

Stel MapReduce. job. Mapss/MapReduce. job. reduceert ten minste het aantal beschik bare containers.  U kunt verder experimenteren door het aantal mappers en verminderers te verhogen om te zien of u betere prestaties krijgt.  Houd er rekening mee dat meer toewijzingen meer overhead hebben, zodat er te veel mappers zijn die de prestaties kunnen verminderen.  

CPU-planning en CPU-isolatie zijn standaard uitgeschakeld zodat het aantal garen containers wordt beperkt door het geheugen.

## <a name="example-calculation"></a>Voorbeeld berekening

We gaan ervan uit dat we een cluster hebben met 8 D14-knoop punten en we willen een I/O-intensieve taak uitvoeren.  Dit zijn de berekeningen die u moet uitvoeren:

**Stap 1: het aantal uitgevoerde taken bepalen**

In dit voor beeld gaan we ervan uit dat onze taak de enige taak is die wordt uitgevoerd.  

**Stap 2: Stel MapReduce. kaart. Memory/MapReduce. Reducing. memory in**

In dit voor beeld voert u een I/O-intensieve taak uit en beslist u dat 3 GB geheugen voor het toewijzen van taken voldoende is.

MapReduce. map. Memory = 3 GB

**Stap 3: de totale hoeveelheid garen bepalen**

Het totale geheugen van het cluster is 8 knoop punten * 96GB van garen geheugen voor een D14 = 768GB

**Stap 4: aantal GARENs**

\# van garen-containers = 768GB beschik bare geheugen/3 GB geheugen = 256

**Stap 5: Stel MapReduce. job. Mapss/MapReduce. job. reduceert**

mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Voor beelden om uit te voeren

Om te laten zien hoe MapReduce wordt uitgevoerd op Data Lake Storage Gen2, hieronder ziet u een voor beeld van een code die op een cluster werd uitgevoerd met de volgende instellingen:

* D14v2 van 16 knoop punten
* Hadoop-cluster met HDI 3,6

Hier volgen enkele voorbeeld opdrachten voor het uitvoeren van MapReduce Teragen, Terasort en Teravalidate.  U kunt deze opdrachten aanpassen op basis van uw resources.

**Teragen**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input
```

**Terasort**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output
```

**Teravalidate**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
```