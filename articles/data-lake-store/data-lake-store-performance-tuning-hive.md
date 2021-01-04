---
title: Prestaties afstemmen-Hive op Azure Data Lake Storage Gen1
description: Meer informatie over het afstemmen van de prestaties voor Hive op HdInsight en Azure Data Lake Storage Gen1. Voor I/O-intensieve query's moet u de component afstemmen om betere prestaties te krijgen.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 082d684ed0a29cb6bf2de9c506886b6d98cf174a
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723778"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Richt lijnen voor het afstemmen van de prestaties van Hive op HDInsight en Azure Data Lake Storage Gen1

De standaard instellingen zijn ingesteld om goede prestaties te bieden in veel verschillende use cases.  Voor I/O-intensieve query's kan de component worden afgestemd om betere prestaties te krijgen met Azure Data Lake Storage Gen1.  

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Data Lake Storage gen1-account**. Zie [aan de slag met Azure data Lake Storage gen1](data-lake-store-get-started-portal.md) voor instructies over het maken van een account.
* **Azure HDInsight-cluster** met toegang tot een Data Lake Storage gen1-account. Zie [een HDInsight-cluster met data Lake Storage gen1 maken](data-lake-store-hdinsight-hadoop-use-portal.md). Zorg ervoor dat Extern bureaublad voor het cluster is ingeschakeld.
* **Hive wordt uitgevoerd op HDInsight**.  Zie [Hive in Hdinsight gebruiken](../hdinsight/hadoop/hdinsight-use-hive.md) voor meer informatie over het uitvoeren van Hive-taken in hdinsight
* **Richt lijnen voor het afstemmen van de prestaties van data Lake Storage gen1**.  Zie [Data Lake Storage gen1 richt lijnen voor het afstemmen van prestaties](./data-lake-store-performance-tuning-guidance.md) voor algemene concepten.

## <a name="parameters"></a>Parameters

Dit zijn de belangrijkste instellingen die u kunt afstemmen voor betere Data Lake Storage Gen1 prestaties:

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

**Component. TEZ. container. size instellen** : in elk knoop punt wordt het geheugen opgegeven door garen. nodemanager. resource. Memory-MB en moet standaard correct worden ingesteld op het HDI-cluster.  Zie dit [bericht](../hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom.md)voor meer informatie over het instellen van het juiste geheugen in garens.

I/O-intensieve workloads kunnen profiteren van meer parallellisatie door de TEZ-container grootte te verlagen. Dit geeft de gebruiker meer containers waarmee de gelijktijdigheid wordt verhoogd.  Sommige Hive-query's vereisen echter een aanzienlijke hoeveelheid geheugen (bijvoorbeeld MapJoin).  Als de taak onvoldoende geheugen heeft, treedt er een uitzonde ring voor onvoldoende geheugen op tijdens runtime.  Als er uitzonde ringen uit het geheugen worden weer gegeven, moet u het geheugen verg Roten.   

Het aantal gelijktijdige taken of parallelle uitvoeringen wordt begrensd door het totale garen geheugen.  Het aantal garen-containers bepaalt hoeveel gelijktijdige taken kunnen worden uitgevoerd.  Als u het garen geheugen per knoop punt wilt vinden, gaat u naar Ambari.  Navigeer naar GARENs en Bekijk het tabblad Configuratie.  Het garen geheugen wordt in dit venster weer gegeven.  

> Totaal aantal GARENs * knoop punten * garen geheugen per knooppunt aantal GARENs = totale garen geheugen/TEZ container grootte

De sleutel voor het verbeteren van de prestaties met behulp van Data Lake Storage Gen1 is om de gelijktijdigheid zo veel mogelijk te verhogen.  TEZ berekent automatisch het aantal taken dat moet worden gemaakt, zodat u dit niet hoeft in te stellen.   

## <a name="example-calculation"></a>Voorbeeld berekening

Stel dat u een D14-cluster van 8 knoop punten hebt.  

> Totaal aantal GARENs * knoop punten * garen geheugen per knoop punt totaal garen geheugen = 8 knoop punten * 96GB = 768GB aantal garen-containers = 768GB/3072MB = 256

## <a name="limitations"></a>Beperkingen

**Data Lake Storage Gen1 beperking** 

Als u de limieten van de band breedte van Data Lake Storage Gen1 bereikt, worden er taak fouten weer gegeven. Dit kan worden geïdentificeerd door beperkings fouten in taak logboeken te observeren.  U kunt de parallellisme verkleinen door de TEZ-container grootte te verhogen.  Neem contact met ons op als u meer gelijktijdigheid wilt voor uw taak.

Als u wilt controleren of u een beperking krijgt, moet u de logboek registratie voor fout opsporing inschakelen op de client. U kunt dit als volgt doen:

1. Plaats de volgende eigenschap in de log4j-eigenschappen in Hive-configuratie. U kunt dit doen vanuit de weer gave Ambari: log4j. logger. com. Microsoft. Azure. datalake. Store = DEBUG start alle knoop punten/service opnieuw op om de configuratie van kracht te laten worden.

2. Als u een beperking krijgt, ziet u de HTTP 429-fout code in het Hive-logboek bestand. Het Hive-logboek bestand bevindt zich in/tmp/ &lt; User &gt; /Hive.log

## <a name="further-information-on-hive-tuning"></a>Meer informatie over Hive-afstemming

Hier volgen enkele blogs waarmee u uw Hive-query's kunt afstemmen:
* [Hive-query's voor Hadoop in HDInsight optimaliseren](../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [Het Hive-query bestand in azure HDInsight coderen](/archive/blogs/bigdatasupport/encoding-the-hive-query-file-in-azure-hdinsight)
* [Ignite Talk to optimize component in HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)