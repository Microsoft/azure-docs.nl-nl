---
title: Azure HDInsight Virtual Network-architectuur
description: Meer informatie over de bronnen die beschikbaar zijn wanneer u een HDInsight-cluster maakt in een Azure-Virtual Network.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 43640a9de91b6b44a236090ff029a49313ee0247
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871670"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Azure HDInsight Virtual Network-architectuur

In dit artikel worden de resources beschreven die aanwezig zijn wanneer u een HDInsight-cluster implementeert in een aangepaste Azure-Virtual Network. Deze informatie helpt u bij het verbinden van on-premises resources met uw HDInsight-cluster in Azure. Zie [Wat is azure Virtual Network?](../virtual-network/virtual-networks-overview.md)voor meer informatie over virtuele Azure-netwerken.

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Resource typen in azure HDInsight-clusters

Azure HDInsight-clusters hebben verschillende typen virtuele machines of knoop punten. Elk knooppunt type speelt een rol in de werking van het systeem. De volgende tabel bevat een overzicht van deze knooppunt typen en hun rollen in het cluster.

| Type | Beschrijving |
| --- | --- |
| Hoofdknooppunt |  Voor alle cluster typen, met uitzonde ring van Apache Storm, hosten de hoofd knooppunten de processen die de uitvoering van de gedistribueerde toepassing beheren. Het hoofd knooppunt is ook het knoop punt dat u kunt SSHen en uitvoeren van toepassingen die vervolgens worden gecoördineerd om te worden uitgevoerd op de cluster resources. Het aantal hoofd knooppunten is vastgesteld op twee voor alle cluster typen. |
| ZooKeeper-knoop punt | Zookeeper coördineert taken tussen de knoop punten die gegevens verwerking uitvoeren. Het geeft ook de leider van het hoofd knooppunt en houdt bij welk hoofd knooppunt een specifieke Master service uitvoert. Het aantal ZooKeeper-knoop punten is op drie opgelost. |
| Worker-knoop punt | Hiermee worden de knoop punten aangeduid die de functionaliteit voor gegevens verwerking ondersteunen. Werk knooppunten kunnen worden toegevoegd aan of verwijderd uit het cluster om de capaciteit van de computer te schalen en de kosten te beheren. |
| R Server Edge-knoop punt | Het knoop punt R Server Edge vertegenwoordigt het knoop punt dat u kunt SSHen en uitvoeren van toepassingen die vervolgens worden gecoördineerd voor uitvoering op de cluster resources. Een Edge-knoop punt neemt niet deel aan de gegevens analyse binnen het cluster. Dit knoop punt fungeert ook als host voor R Studio Server, zodat u R-toepassingen kunt uitvoeren met behulp van een browser. |
| Regio knooppunt | Voor het HBase-cluster type voert het regio-knoop punt (ook wel een gegevens knooppunt genoemd) de regio server uit. Regio servers fungeren en beheren een deel van de gegevens die worden beheerd door HBase. Regio knooppunten kunnen worden toegevoegd aan of verwijderd uit het cluster om de capaciteit van de computer te schalen en de kosten te beheren.|
| Nimbus-knoop punt | Voor het Storm-cluster type biedt het Nimbus-knoop punt vergelijk bare functionaliteit als het hoofd knooppunt. Het Nimbus-knoop punt wijst taken toe aan andere knoop punten in een cluster via Zookeeper, die de uitvoering van Storm-topologieën coördineert. |
| Supervisor knooppunt | Voor het Storm-cluster type voert het knoop punt Super Visor de instructies uit die zijn verschaft door het knoop punt Nimbus om de verwerking uit te voeren. |

## <a name="resource-naming-conventions"></a>Resource naamgevings conventies

Gebruik FQDN-namen (FULLy Qualified Domain names) bij het adresseren van knoop punten in uw cluster. U kunt de FQDN-namen voor verschillende knooppunt typen in uw cluster ophalen met behulp van de [Ambari-API](hdinsight-hadoop-manage-ambari-rest-api.md).

Deze FQDN-namen hebben het formulier `<node-type-prefix><instance-number>-<abbreviated-clustername>.<unique-identifier>.cx.internal.cloudapp.net` .

De `<node-type-prefix>` wordt *HN* voor hoofd knooppunten, *wn* voor worker-knoop punten en *Zn* voor Zookeeper-knoop punten.

Als u alleen de hostnaam nodig hebt, gebruikt u alleen het eerste deel van de FQDN-naam: `<node-type-prefix><instance-number>-<abbreviated-clustername>`

## <a name="basic-virtual-network-resources"></a>Basis bronnen voor virtueel netwerk

In het volgende diagram ziet u de plaatsing van HDInsight-knoop punten en netwerk resources in Azure.

:::image type="content" source="./media/hdinsight-virtual-network-architecture/hdinsight-vnet-diagram.png" alt-text="Diagram van HDInsight-entiteiten die zijn gemaakt in een aangepast Azure-VNET" border="false":::

De standaard resources in een Azure-Virtual Network bevatten de cluster knooppunt typen die in de vorige tabel worden vermeld. En netwerk apparaten die ondersteuning bieden voor communicatie tussen het virtuele netwerk en externe netwerken.

De volgende tabel bevat een overzicht van de negen cluster knooppunten die zijn gemaakt wanneer HDInsight wordt geïmplementeerd in een aangepaste Azure-Virtual Network.

| Resourcetype | Nummer aanwezig | Details |
| --- | --- | --- |
|Hoofdknooppunt | twee |    |
|Zookeeper-knooppunt | drie | |
|Worker-knoop punt | twee | Dit aantal kan variëren afhankelijk van de configuratie van het cluster en het schalen. Er zijn mini maal drie worker-knoop punten nodig voor Apache Kafka.  |
|Gateway-knooppunt | twee | Gateway knooppunten zijn virtuele Azure-machines die zijn gemaakt op Azure, maar die niet zichtbaar zijn in uw abonnement. Neem contact op met de ondersteuning als u deze knoop punten opnieuw moet opstarten. |

De volgende netwerk bronnen worden automatisch gemaakt in het virtuele netwerk dat wordt gebruikt met HDInsight:

| Netwerk bron | Nummer aanwezig | Details |
| --- | --- | --- |
|Load balancer | drie | |
|Netwerkinterfaces | negen | Deze waarde is gebaseerd op een normaal cluster, waarbij elk knoop punt een eigen netwerk interface heeft. De negen interfaces zijn voor: twee hoofd knooppunten, drie Zookeeper-knoop punten, twee worker-knoop punten en twee gateway knooppunten die worden vermeld in de vorige tabel. |
|Openbare IP-adressen | twee |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Eind punten voor verbinding maken met HDInsight

U kunt op drie manieren toegang krijgen tot uw HDInsight-cluster:

- Een HTTPS-eind punt buiten het virtuele netwerk op `CLUSTERNAME.azurehdinsight.net` .
- Een SSH-eind punt voor rechtstreekse verbinding met de hoofd knooppunt op `CLUSTERNAME-ssh.azurehdinsight.net` .
- Een HTTPS-eind punt in het virtuele netwerk `CLUSTERNAME-int.azurehdinsight.net` . Let op de " `-int` " in deze URL. Dit eind punt wordt omgezet naar een privé-IP-adres in dat virtuele netwerk en is niet toegankelijk via het open bare Internet.

Deze drie eind punten krijgen elk een load balancer.

Open bare IP-adressen worden ook gegeven aan de twee eind punten die de verbinding van buiten het virtuele netwerk toestaan.

1. Er wordt één openbaar IP-adres toegewezen aan de load balancer voor de Fully Qualified Domain Name (FQDN) die moet worden gebruikt om verbinding te maken met het cluster via internet `CLUSTERNAME.azurehdinsight.net` .
1. Het tweede open bare IP-adres wordt gebruikt voor de domein naam SSH only `CLUSTERNAME-ssh.azurehdinsight.net` .

## <a name="next-steps"></a>Volgende stappen

- [Binnenkomend verkeer naar HDInsight-clusters in een virtueel netwerk met een persoonlijk eind punt beveiligen](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
