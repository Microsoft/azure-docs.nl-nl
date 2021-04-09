---
title: Cluster capaciteit plannen in azure HDInsight
description: Bepaal de belangrijkste vragen voor capaciteit en prestaties plannen van een Azure HDInsight-cluster.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: 45cfa90f2156dba87cbec2b6313bc24e5d030572
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933224"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Capaciteitsplanning voor HDInsight-clusters

Voordat u een HDInsight-cluster implementeert, moet u de beoogde cluster capaciteit plannen door de benodigde prestaties en schaal te bepalen. Deze planning helpt zowel de bruikbaarheid als de kosten te optimaliseren. Sommige beslissingen over cluster capaciteit kunnen niet worden gewijzigd na de implementatie. Als de prestatie parameters veranderen, kan een cluster worden gedemonteerd en opnieuw worden gemaakt zonder dat de opgeslagen gegevens verloren gaan.

De belangrijkste vragen om de capaciteits planning te vragen zijn:

* In welke geografische regio moet u uw cluster implementeren?
* Hoeveel opslag ruimte hebt u nodig?
* Welk cluster type moet u implementeren?
* Welke grootte en welk type virtuele machine (VM) moet uw cluster knooppunten gebruiken?
* Hoeveel worker-knoop punten moet uw cluster hebben?

## <a name="choose-an-azure-region"></a>Een Azure-regio kiezen

De Azure-regio bepaalt waar uw cluster fysiek is ingericht. Voor een minimale latentie van lees-en schrijf bewerkingen moet het cluster zich in de buurt van uw gegevens bevinden.

HDInsight is beschikbaar in veel Azure-regio's. Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight)om de dichtstbijzijnde regio te vinden.

## <a name="choose-storage-location-and-size"></a>Opslag locatie en-grootte kiezen

### <a name="location-of-default-storage"></a>Locatie van standaard opslag

De standaard opslag, een Azure Storage account of Azure Data Lake Storage, moet zich op dezelfde locatie bestaan als uw cluster. Azure Storage is beschikbaar op alle locaties. Data Lake Storage Gen1 is beschikbaar in sommige regio's: Bekijk de huidige [Data Lake Storage Beschik baarheid](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

### <a name="location-of-existing-data"></a>Locatie van bestaande gegevens

Als u een bestaand opslag account of Data Lake Storage wilt gebruiken als de standaard opslag van uw cluster, moet u uw cluster op diezelfde locatie implementeren.

### <a name="storage-size"></a>Opslag grootte

U kunt op een geïmplementeerd cluster extra Azure Storage accounts koppelen of andere Data Lake Storage gebruiken. Al uw opslag accounts moeten op dezelfde locatie als uw cluster wonen. Een Data Lake Storage kan zich op een andere locatie bevinden, maar grote afstanden kunnen enige latentie veroorzaken.

Azure Storage heeft een aantal [capaciteits limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits), terwijl data Lake Storage gen1 bijna onbeperkt is.

Een cluster kan toegang krijgen tot een combi natie van verschillende opslag accounts. Typische voor beelden zijn:

* Wanneer de hoeveelheid gegevens waarschijnlijk de opslag capaciteit van één Blob Storage-container overschrijdt.
* Wanneer de snelheid van toegang tot de BLOB-container de drempel waarde kan overschrijden wanneer er sprake is van beperking.
* Wanneer u gegevens wilt maken, bent u al geüpload naar een BLOB-container die beschikbaar is voor het cluster.
* Als u verschillende onderdelen van de opslag ruimte wilt isoleren om redenen van beveiliging, of om het beheer te vereenvoudigen.

Gebruik slechts één container per opslag account voor betere prestaties.

## <a name="choose-a-cluster-type"></a>Een cluster type kiezen

Het cluster type bepaalt de werk belasting die uw HDInsight-cluster is geconfigureerd om te worden uitgevoerd. Typen bevatten [Apache Hadoop](./hadoop/apache-hadoop-introduction.md), [Apache Storm](./storm/apache-storm-overview.md), [Apache Kafka](./kafka/apache-kafka-introduction.md)of [Apache Spark](./spark/apache-spark-overview.md). Zie [Inleiding tot Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight)voor een gedetailleerde beschrijving van de beschik bare cluster typen. Elk cluster type heeft een specifieke implementatie topologie met vereisten voor de grootte en het aantal knoop punten.

## <a name="choose-the-vm-size-and-type"></a>De grootte en het type van de virtuele machine kiezen

Elk cluster type heeft een reeks knooppunt typen en elk knooppunt type heeft specifieke opties voor de VM-grootte en het bijbehorende type.

Als u de optimale cluster grootte voor uw toepassing wilt bepalen, kunt u een bench Mark-cluster capaciteit opgeven en de opgegeven grootte verg Roten. U kunt bijvoorbeeld een gesimuleerde werk belasting of een *Canarische query* gebruiken. Voer uw gesimuleerde workloads uit op verschillende clusters. Verhoog de grootte geleidelijk tot de beoogde prestaties zijn bereikt. Een Canarische query kan regel matig worden ingevoegd tussen de andere productie query's om weer te geven of het cluster voldoende bronnen heeft.

Zie [de juiste VM-grootte voor uw cluster selecteren](hdinsight-selecting-vm-size.md)voor meer informatie over het kiezen van de juiste VM-serie voor uw werk belasting.

## <a name="choose-the-cluster-scale"></a>De cluster schaal kiezen

De schaal van een cluster wordt bepaald door het aantal VM-knoop punten. Voor alle cluster typen zijn er knooppunt typen met een specifieke schaal en knooppunt typen die ondersteuning bieden voor uitschalen. Een cluster kan bijvoorbeeld precies drie [Apache ZooKeeper](https://zookeeper.apache.org/) knoop punten of twee hoofd knooppunten hebben. Worker-knoop punten die gegevens verwerking uitvoeren in een gedistribueerde manier, profiteren van de extra worker-knoop punten.

Afhankelijk van het type cluster, voegt het aantal worker-knoop punten extra reken capaciteit toe (zoals meer kern geheugens). Meer knoop punten verhogen het totale geheugen dat nodig is voor het hele cluster om in-Memory opslag te ondersteunen van de gegevens die worden verwerkt. Net als bij de keuze van de grootte en het type van de virtuele machine, wordt het selecteren van de juiste cluster schaal meestal empirisch bereikt. Gebruik gesimuleerde werk belastingen of Canarische query's.

U kunt uw cluster uitschalen om te voldoen aan de maximale belasting vereisten. Vervolgens kunt u een back-up maken als deze extra knoop punten niet meer nodig zijn. Met de functie voor automatisch [schalen](hdinsight-autoscale-clusters.md) kunt u uw cluster op basis van vooraf bepaalde metrische gegevens en tijdstippen schalen. Zie [HDInsight-clusters schalen](hdinsight-scaling-best-practices.md)voor meer informatie over het hand matig schalen van clusters.

### <a name="cluster-lifecycle"></a>Cluster levenscyclus

Er worden kosten in rekening gebracht voor de levens duur van een cluster. Als er alleen specifieke tijden zijn waarop u uw cluster nodig hebt, [maakt u clusters op aanvraag met behulp van Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). U kunt ook Power shell-scripts maken die uw cluster inrichten en verwijderen, en deze scripts vervolgens plannen met behulp van [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Wanneer een cluster wordt verwijderd, wordt de standaard Hive-metastore ook verwijderd. Als u de meta Store voor het opnieuw maken van het volgende cluster wilt behouden, gebruikt u een externe metagegevensarchiefmethode, zoals Azure data base of [Apache Oozie](https://oozie.apache.org/).

### <a name="isolate-cluster-job-errors"></a>Cluster taak fouten isoleren

Soms kunnen er fouten optreden vanwege de parallelle uitvoering van meerdere kaarten en het verminderen van de onderdelen op een cluster met meerdere knoop punten. Probeer gedistribueerde tests om het probleem te isoleren. Voer gelijktijdige meerdere taken uit op een knoop punt cluster met één werk nemer. Vouw vervolgens deze benadering uit om meerdere taken gelijktijdig uit te voeren op clusters met meer dan één knoop punt. Als u een HDInsight-cluster met één knoop punt in azure wilt maken, gebruikt u de *`Custom(size, settings, apps)`* optie en gebruikt u een waarde van 1 voor het *aantal worker-knoop punten* in de sectie **cluster grootte** bij het inrichten van een nieuw cluster in de portal.

## <a name="quotas"></a>Quota

Zie [aanvragen van quota verhogen](quota-increase-request.md)voor meer informatie over het beheren van abonnements quota's.

## <a name="next-steps"></a>Volgende stappen

* [Stel clusters in hdinsight in met Apache Hadoop, Spark, Kafka en meer](hdinsight-hadoop-provision-linux-clusters.md): meer informatie over het instellen en configureren van clusters in hdinsight.
* [Prestaties van het cluster bewaken](hdinsight-key-scenarios-to-monitor.md): meer informatie over de belangrijkste scenario's voor het bewaken van uw HDInsight-cluster die van invloed kunnen zijn op de capaciteit van uw cluster.
