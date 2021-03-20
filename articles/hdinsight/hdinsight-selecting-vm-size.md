---
title: De juiste VM-grootte voor uw Azure HDInsight-cluster selecteren
description: Meer informatie over het selecteren van de juiste VM-grootte voor uw HDInsight-cluster.
keywords: VM-grootten, cluster grootte, cluster configuratie
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 51043f0a1009994528783a1b56ec5ccec68e99b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931786"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>De juiste VM-grootte voor uw Azure HDInsight-cluster selecteren

In dit artikel wordt beschreven hoe u de juiste VM-grootte kunt selecteren voor de verschillende knoop punten in uw HDInsight-cluster. 

Begin door te weten hoe de eigenschappen van een virtuele machine, zoals CPU-verwerking, RAM-grootte en netwerk latentie, van invloed zijn op de verwerking van uw workloads. Denk vervolgens na over uw toepassing en hoe deze overeenkomt met de verschillende VM-families die zijn geoptimaliseerd voor. Zorg ervoor dat de VM-familie die u wilt gebruiken, compatibel is met het cluster type dat u wilt implementeren. Zie door [Azure HDInsight ondersteunde knooppunt configuraties](hdinsight-supported-node-configuration.md)voor een lijst met alle ondersteunde en aanbevolen VM-grootten voor elk cluster type. Ten slotte kunt u een benchmark procedure gebruiken om een aantal voorbeeld workloads te testen en te controleren welke SKU binnen die familie geschikt is voor u.

Zie [capaciteits planning voor HDInsight-clusters](hdinsight-capacity-planning.md)voor meer informatie over het plannen van andere aspecten van het cluster, zoals het selecteren van een opslag type of cluster grootte.

## <a name="vm-properties-and-big-data-workloads"></a>VM-eigenschappen en big data-workloads

De grootte en het type van de virtuele machine zijn afhankelijk van CPU-verwerkings kracht, RAM-grootte en netwerk latentie:

- CPU: de grootte van de virtuele machine bepaalt het aantal kernen. Hoe meer kernen, des te groter de mate van parallelle reken kracht van elk knoop punt kan worden gerealiseerd. Daarnaast hebben sommige VM-typen snellere kernen.

- RAM: de VM-grootte bepaalt ook de hoeveelheid RAM-geheugen dat beschikbaar is in de virtuele machine. Voor werk belastingen waarbij gegevens in het geheugen worden opgeslagen voor verwerking, in plaats van de schijf te lezen, moet u ervoor zorgen dat uw worker-knoop punten voldoende geheugen hebben om de gegevens te passen.

- Netwerk: voor de meeste cluster typen worden de gegevens die door het cluster worden verwerkt, niet op de lokale schijf opgeslagen, maar in een externe opslag service, zoals Data Lake Storage of Azure Storage. Houd rekening met de netwerk bandbreedte en door Voer tussen de VM van het knoop punt en de opslag service. De netwerk bandbreedte die beschikbaar is voor een virtuele machine neemt doorgaans toe met grotere grootten. Zie [overzicht van VM-grootten](../virtual-machines/sizes.md)voor meer informatie.

## <a name="understanding-vm-optimization"></a>Meer informatie over VM-optimalisatie

Virtuele-machine families in azure zijn geoptimaliseerd voor verschillende use cases. In de onderstaande tabel vindt u enkele van de populairste use cases en de VM-families die overeenkomen.

| Type                     | Grootten           |    Beschrijving       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Instap niveau](../virtual-machines/sizes-general.md)          | A, Av2  | Beschikken over CPU-prestaties en geheugen configuraties die het meest geschikt zijn voor workloads op instap niveau, zoals ontwikkelen en testen. Ze zijn voordelig en bieden een goedkope optie om aan de slag te gaan met Azure. |
| [Algemeen gebruik](../virtual-machines/sizes-general.md)          | D, DSv2, dv2  | Evenwichtige CPU-geheugenverhouding. Dit is ideaal voor testen en ontwikkelen, voor kleine tot middelgrote databases, en webservers met weinig tot gemiddeld verkeer. |
| [Geoptimaliseerde rekenkracht](../virtual-machines/sizes-compute.md)        | F           | Hoge CPU-geheugenverhouding. Geschikt voor webservers met gemiddeld verkeer, netwerk apparaten, batch processen en toepassings servers.        |
| [Geoptimaliseerd voor geheugen](../virtual-machines/sizes-memory.md)         | Esv3, Ev3  | Hoge geheugen-CPU-verhouding. Zeer geschikt voor relationele databaseservers, middelgrote tot grote caches, en analysefuncties in het geheugen.                 |

- Zie [prijzen voor hdinsight](https://azure.microsoft.com/pricing/details/hdinsight/)voor meer informatie over de prijzen van beschik bare VM-exemplaren in de regio's die door hdinsight worden ondersteund.

## <a name="cost-saving-vm-types-for-light-workloads"></a>Kosten besparing van VM-typen voor lichte workloads

Als er lichte verwerkings vereisten gelden, kan de [F-serie](https://azure.microsoft.com/blog/f-series-vm-size/) een goede keuze zijn om aan de slag te gaan met HDInsight. Tegen een lagere prijs per uur levert de F-serie de beste prijs/kwaliteit-verhouding van de Azure-portfolio, gebaseerd op de ACU (Azure Compute Unit) per vCPU.

De volgende tabel beschrijft de cluster typen en knooppunt typen die kunnen worden gemaakt met de virtuele machines uit de Fsv2-serie.

| Clustertype | Versie | Worker-knoop punt | Hoofd knooppunt | Zookeeper-knoop punt |
|---|---|---|---|---|
| Spark | Alles | F4 en hoger | nee | nee |
| Hadoop | Alles | F4 en hoger | nee | nee |
| Kafka | Alles | F4 en hoger | nee | nee |
| HBase | Alles | F4 en hoger | nee | nee |
| LLAP | geblokkeerd | nee | nee | nee |
| Storm | geblokkeerd | nee | nee | nee |
| ML-service | ALLEEN HDI 3,6 | F4 en hoger | nee | nee |

Zie [VM-grootten](https://azure.microsoft.com/blog/f-series-vm-size/)van de f-serie voor een overzicht van de specificaties van elke SKU uit de f-serie.

## <a name="benchmarking"></a>Benchmarking

Benchmarking is het proces van het uitvoeren van gesimuleerde werk belastingen op verschillende Vm's om te meten hoe goed ze worden uitgevoerd voor uw productie workloads. 

Zie [cluster capaciteit plannen in azure HDInsight ](hdinsight-capacity-planning.md#choose-the-vm-size-and-type)voor meer informatie over de benchmarking voor VM-sku's en cluster groottes.

## <a name="next-steps"></a>Volgende stappen

- [Ondersteunde knooppunt configuraties van Azure HDInsight](hdinsight-supported-node-configuration.md)
- [Sizes for Linux virtual machines in Azure](../virtual-machines/sizes.md)