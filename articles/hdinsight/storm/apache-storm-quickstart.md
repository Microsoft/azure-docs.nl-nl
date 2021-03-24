---
title: 'Quickstart: Apache Storm-topologie maken/beheren - Azure HDInsight'
description: In de snelstart leert u hoe u een Apache Storm-topologie kunt maken en bewaken in Azure HDInsight.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.custom: mvc
ms.openlocfilehash: 73b0434065b06f25320a0666937fd7969c863b33
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870208"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Quickstart: Apache Storm-topologie maken/bewaken in Azure HDInsight

Apache Storm is een gedistribueerd, schaalbaar, fouttolerant en realtime berekeningssysteem voor het verwerken van gegevensstromen. Met Storm in Azure HDInsight kunt u een op een cloud gebaseerd Storm-cluster maken dat in realtime big data-analyses uitvoert.

In deze snelstart gebruikt u een voorbeeld uit het Apache [storm-starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter)-project om een Apache Storm-topologie te maken en bewaken voor een bestaand Apache Storm-cluster.

## <a name="prerequisites"></a>Vereisten

* Een Apache Storm-cluster in HDInsight. Zie [Apache Hadoop-clusters maken met behulp van de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en selecteer **Storm** voor **Clustertype**.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>De topologie maken

1. Maak verbinding met uw Storm-cluster. Bewerk de onderstaande opdracht door `CLUSTERNAME` te vervangen door de naam van uw Storm-cluster. Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Het **WordCount**-voorbeeld is op uw HDInsight-cluster opgenomen op `/usr/hdp/current/storm-client/contrib/storm-starter/`. De topologie genereert willekeurige zinnen en telt hoe vaak woorden voorkomen. Gebruik de volgende opdracht om de **wordcount**-topologie op het cluster te starten:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>De topologie bewaken

Storm biedt een webinterface voor het werken met actieve topologieën en is opgenomen in uw HDInsight-cluster.

Voer de volgende stappen uit voor het bewaken van de topologie met behulp van de Storm-gebruikersinterface:

1. Als u de Storm-gebruikersinterface wilt weergeven, opent u `https://CLUSTERNAME.azurehdinsight.net/stormui` in een webbrowser. Vervang `CLUSTERNAME` door de naam van uw cluster.

2. Selecteer onder **Topology summary** de vermelding **wordcount** in de kolom **Name**. Er wordt informatie over de topologie weergegeven.

    :::image type="content" source="./media/apache-storm-quickstart/hdi-topology-summary.png" alt-text="Storm-dashboard met informatie over de Storm-Starter WordCount-topologie." border="true":::

    De nieuwe pagina bevat de volgende informatie:

    |Eigenschap | Beschrijving |
    |---|---|
    |Topology stats|Basisinformatie over de topologieprestaties, geordend in tijdvensters. Wanneer er een specifiek tijdvenster wordt geselecteerd, verandert het tijdvenster voor informatie die wordt weergegeven in andere gedeelten van de pagina.|
    |Spouts|Basisinformatie over spouts, met inbegrip van de laatste fout die door elke spout is geretourneerd.|
    |Bolts|Basisinformatie over bolts.|
    |Topology configuration|Gedetailleerde informatie over de topologieconfiguratie.|
    |Activate|Hiermee wordt de verwerking van een gedeactiveerde topologie hervat.|
    |Deactivate|Hiermee wordt een actieve topologie onderbroken.|
    |Rebalance|Hiermee wordt de parallelle uitvoering van de topologie aangepast. Nadat u het aantal knooppunten in het cluster hebt gewijzigd, moet u actieve topologieën opnieuw verdelen. Met het opnieuw verdelen wordt de parallelle uitvoering aangepast om te compenseren voor het toegenomen/afgenomen aantal knooppunten in het cluster. Zie [Inzicht in de parallelle uitvoering van een Apache Storm-topologie](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) voor meer informatie.|
    |Kill|Hiermee wordt een Storm-topologie na de opgegeven time-out beëindigd.|

3. Selecteer op deze pagina een item in de sectie **Spouts** of **Bolts**. Er wordt informatie over het geselecteerde onderdeel weergegeven.

    :::image type="content" source="./media/apache-storm-quickstart/hdi-component-summary.png" alt-text="Storm-dashboard met informatie over de geselecteerde onderdelen." border="true":::

    De nieuwe pagina geeft de volgende informatie weer:

    |Eigenschap | Beschrijving |
    |---|---|
    |Spout/Bolt stats|Basisinformatie over de prestaties van de diverse onderdelen, geordend in tijdvensters. Wanneer er een specifiek tijdvenster wordt geselecteerd, verandert het tijdvenster voor informatie die wordt weergegeven in andere gedeelten van de pagina.|
    |Input stats (alleen bolts)|Informatie over onderdelen die gegevens produceren die worden verbruikt door de bolt.|
    |Output stats|Informatie over gegevens die door deze bolt zijn gegenereerd.|
    |Executors|Informatie over exemplaren van dit onderdeel.|
    |Errors|Fouten die door dit onderdeel zijn gegenereerd.|

4. Als u details voor een specifiek exemplaar van het onderdeel wilt weergeven, selecteert u een item in de kolom **Port** in de sectie **Executor** terwijl u de details van een spout of bolt bekijkt.

```output
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]
```

In dit voorbeeld komt het woord **seven** 1.493.957 keer voor. Dit getal is het aantal keer dat het woord is gevonden sinds deze topologie is gestart.

## <a name="stop-the-topology"></a>De topologie stoppen

Ga terug naar de pagina **Topology summary** voor de word-count-topologie en klik vervolgens op de knop **Kill** in de sectie **Topology actions**. Wanneer hierom wordt gevraagd, voert u 10 in voor het aantal seconden dat moet worden gewacht voordat de topologie wordt gestopt. Na de time-outperiode wordt de topologie niet meer weergegeven als u het gedeelte **Storm UI** van het dashboard bezoekt.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de quickstart hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Als u een cluster wilt verwijderen, raadpleegt u [HDInsight-cluster verwijderen met behulp van uw browser, PowerShell of de Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een voorbeeld uit het Apache [storm-starter](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter)-project gebruikt om een Apache Storm-topologie te maken en bewaken voor een bestaand Apache Storm-cluster. Ga naar het volgende artikel voor meer informatie over de basisprincipes van het beheren en bewaken van Apache Storm-topologieën.

> [!div class="nextstepaction"]
>[Apache Storm-topologieën implementeren en beheren in Azure HDInsight](./apache-storm-deploy-monitor-topology-linux.md)