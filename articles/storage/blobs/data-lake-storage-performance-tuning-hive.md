---
title: 'Prestaties afstemmen: Hive, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Informatie over afstemmings richtlijnen voor I/O-intensieve query's met behulp van Hive, HDInsight en Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4b1e5dd3c72122ade2fd4d4092bb18a7acf215f5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912940"
---
# <a name="tune-performance-hive-hdinsight--azure-data-lake-storage-gen2"></a>Prestaties afstemmen: Hive, HDInsight & Azure Data Lake Storage Gen2

De standaard instellingen zijn ingesteld om goede prestaties te bieden in veel verschillende use cases.  Voor I/O-intensieve query's kan de component worden afgestemd om betere prestaties te krijgen met Azure Data Lake Storage Gen2.  

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Data Lake Storage Gen2-account**. Zie [Quick Start: een Azure data Lake Storage Gen2 Storage-account maken](../common/storage-account-create.md) voor instructies over het maken van een
* **Azure HDInsight-cluster** met toegang tot een Data Lake Storage Gen2-account. Zie [Azure data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)
* **Hive wordt uitgevoerd op HDInsight**.  Zie [Hive in Hdinsight gebruiken](../../hdinsight/hadoop/hdinsight-use-hive.md) voor meer informatie over het uitvoeren van Hive-taken in hdinsight
* **Richt lijnen voor het afstemmen van de prestaties van data Lake Storage Gen2**.  Zie [Data Lake Storage Gen2 richt lijnen voor het afstemmen van prestaties](data-lake-storage-performance-tuning-guidance.md) voor algemene concepten.

## <a name="parameters"></a>Parameters

Dit zijn de belangrijkste instellingen die u kunt afstemmen voor betere Data Lake Storage Gen2 prestaties:

* **Hive. TEZ. container. size** : de hoeveelheid geheugen die door elke taak wordt gebruikt

* **TEZ. grouping. min-grootte** – minimale grootte van elke Mapper

* **TEZ. grouping. max-size** : maximum grootte van elke Mapper

* **hive.exec. reducr. bytes. per. versmaller** : grootte van elke verminderr

**component. TEZ. container. size** : de grootte van de container bepaalt hoeveel geheugen beschikbaar is voor elke taak.  Dit is de belangrijkste invoer voor het beheren van de gelijktijdigheid in Hive.  

**TEZ. grouping. min-grootte** : met deze para meter kunt u de minimale grootte van elke Mapper instellen.  Als het aantal mappers dat TEZ kiest kleiner is dan de waarde van deze para meter, gebruikt TEZ de hier ingestelde waarde.

**TEZ. grouping. max-size** – met de para meter kunt u de maximale grootte van elke Mapper instellen.  Als het aantal mappers dat TEZ kiest groter is dan de waarde van deze para meter, gebruikt TEZ de hier ingestelde waarde.

**hive.exec. reducr. bytes. per. versmaller** : met deze para meter wordt de grootte van elke verminderr ingesteld.  Standaard is elke reducer 256 MB.  

## <a name="guidance"></a>Hulp

**Stel hive.exec. reducr. bytes. per. versmaller** : de standaard waarde werkt goed als de gegevens niet zijn gecomprimeerd.  Voor gegevens die zijn gecomprimeerd, moet u de grootte van de reducer verkleinen.  

**Component. TEZ. container. size instellen** : in elk knoop punt wordt het geheugen opgegeven door garen. nodemanager. resource. Memory-MB en moet standaard correct worden ingesteld op het HDI-cluster.  Zie dit [bericht](../../hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom.md)voor meer informatie over het instellen van het juiste geheugen in garens.

I/O-intensieve workloads kunnen profiteren van meer parallellisatie door de TEZ-container grootte te verlagen. Dit geeft de gebruiker meer containers waarmee de gelijktijdigheid wordt verhoogd.  Sommige Hive-query's vereisen echter een aanzienlijke hoeveelheid geheugen (bijvoorbeeld MapJoin).  Als de taak onvoldoende geheugen heeft, treedt er een uitzonde ring voor onvoldoende geheugen op tijdens runtime.  Als er uitzonde ringen uit het geheugen worden weer gegeven, moet u het geheugen verg Roten.   

Het aantal gelijktijdige taken of parallelle uitvoeringen wordt begrensd door het totale garen geheugen.  Het aantal garen-containers bepaalt hoeveel gelijktijdige taken kunnen worden uitgevoerd.  Als u het garen geheugen per knoop punt wilt vinden, gaat u naar Ambari.  Navigeer naar GARENs en Bekijk het tabblad Configuratie.  Het garen geheugen wordt in dit venster weer gegeven.  

- Totaal aantal GARENs = knoop punten * garen geheugen per knoop punt
- \# van garen-containers = totale garen geheugen/TEZ container grootte

De sleutel voor het verbeteren van de prestaties met behulp van Data Lake Storage Gen2 is om de gelijktijdigheid zo veel mogelijk te verhogen.  TEZ berekent automatisch het aantal taken dat moet worden gemaakt, zodat u dit niet hoeft in te stellen.   

## <a name="example-calculation"></a>Voorbeeld berekening

Stel dat u een D14-cluster van 8 knoop punten hebt.  

- Totaal aantal GARENs = knoop punten * garen geheugen per knoop punt
- Totaal aantal GARENs = 8 knoop punten * 96GB = 768GB
- \# van garen-containers = 768GB/3072MB = 256

## <a name="further-information-on-hive-tuning"></a>Meer informatie over Hive-afstemming

Hier volgen enkele blogs waarmee u uw Hive-query's kunt afstemmen:
* [Hive-query's voor Hadoop in HDInsight optimaliseren](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [Apache Hive-query's in Azure HDInsight optimaliseren](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [Ignite Talk to optimize component in HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)