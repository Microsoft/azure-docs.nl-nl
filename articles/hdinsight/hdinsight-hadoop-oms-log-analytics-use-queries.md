---
title: Query's uitvoeren op Azure Monitor logboeken voor het bewaken van Azure HDInsight-clusters
description: Meer informatie over het uitvoeren van query's in Azure Monitor logboeken voor het bewaken van taken die worden uitgevoerd in een HDInsight-cluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: f9213f36ec33939c3df3b56d21822aa3b6a17c03
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945616"
---
# <a name="query-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor logboeken voor het controleren van HDInsight-clusters doorzoeken

Meer informatie over de basis scenario's voor het gebruik van Azure Monitor logboeken voor het bewaken van Azure HDInsight-clusters:

* [Metrische gegevens van HDInsight-cluster analyseren](#analyze-hdinsight-cluster-metrics)
* [Gebeurtenis waarschuwingen maken](#create-alerts-for-tracking-events)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

U moet een HDInsight-cluster hebben geconfigureerd om Azure Monitor-logboeken te gebruiken en u hebt de HDInsight-cluster-specifieke Azure Monitor logboeken voor het controleren van oplossingen toegevoegd aan de werk ruimte. Zie [Azure monitor-Logboeken gebruiken met HDInsight-clusters](hdinsight-hadoop-oms-log-analytics-tutorial.md)voor instructies.

## <a name="analyze-hdinsight-cluster-metrics"></a>Metrische gegevens van HDInsight-cluster analyseren

Meer informatie over het zoeken naar specifieke metrische gegevens voor uw HDInsight-cluster.

1. Open de Log Analytics-werk ruimte die is gekoppeld aan uw HDInsight-cluster via de Azure Portal.
1. Selecteer **Logboeken** onder **Algemeen**.
1. Typ de volgende query in het zoekvak om te zoeken naar alle metrische gegevens voor alle beschik bare gegevens voor alle HDInsight-clusters die zijn geconfigureerd voor het gebruik van Azure Monitor logboeken en selecteer vervolgens **uitvoeren**. Bekijk de resultaten.

    ```kusto
    search *
    ```

    ![Apache Ambari Analytics alle metrische gegevens doorzoeken](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-all-metrics.png "Alle metrische gegevens zoeken")

1. Selecteer in het menu links het tabblad **filter** .

1. Onder **type**, selecteer **heartbeat**. Selecteer vervolgens **Toep assen & uitvoeren**.

    ![Log Analytics-specifieke metrische gegevens zoeken](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-search-specific-metrics.png "Specifieke metrische gegevens zoeken")

1. U ziet dat de query in het tekstvak verandert in:

    ```kusto
    search *
    | where Type == "Heartbeat"
    ```

1. U kunt dieper met behulp van de beschik bare opties in het menu links. Bijvoorbeeld:

    - Logboeken van een specifiek knoop punt weer geven:

        ![Zoeken naar specifieke fouten output1](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-node.png "Zoeken naar specifieke fouten output1")

    - Logboeken op bepaalde momenten bekijken:

        ![Zoeken naar specifieke fouten output2](./media/hdinsight-hadoop-oms-log-analytics-use-queries/log-analytics-specific-time.png "Zoeken naar specifieke fouten output2")

1. Selecteer **Toep assen & uitvoeren** en Bekijk de resultaten. Houd er ook rekening mee dat de query is bijgewerkt naar:

    ```kusto
    search *
    | where Type == "Heartbeat"
    | where (Computer == "zk2-myhado") and (TimeGenerated == "2019-12-02T23:15:02.69Z" or TimeGenerated == "2019-12-02T23:15:08.07Z" or TimeGenerated == "2019-12-02T21:09:34.787Z")
    ```

### <a name="additional-sample-queries"></a>Aanvullende voorbeeld query's

Een voorbeeld query op basis van het gemiddelde van de resources die worden gebruikt in een interval van 10 minuten, gecategoriseerd op cluster naam:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize AggregatedValue = avg(UsedAMResourceMB_d) by ClusterName_s, bin(TimeGenerated, 10m)
```

In plaats van te verfijnen op basis van het gemiddelde van gebruikte resources, kunt u de volgende query gebruiken om de resultaten te verfijnen op basis van de gebruikte maximum bronnen (evenals negen tigste en 95e percentiel) in een venster van 10 minuten:

```kusto
search in (metrics_resourcemanager_queue_root_default_CL) * 
| summarize ["max(UsedAMResourceMB_d)"] = max(UsedAMResourceMB_d), ["pct95(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 95), ["pct90(UsedAMResourceMB_d)"] = percentile(UsedAMResourceMB_d, 90) by ClusterName_s, bin(TimeGenerated, 10m)
```

## <a name="create-alerts-for-tracking-events"></a>Waarschuwingen voor het bijhouden van gebeurtenissen maken

De eerste stap bij het maken van een waarschuwing is het aanroepen van een query op basis waarvan de waarschuwing wordt geactiveerd. U kunt elke query gebruiken waarvoor u een waarschuwing wilt maken.

1. Open de Log Analytics-werk ruimte die is gekoppeld aan uw HDInsight-cluster via de Azure Portal.
1. Selecteer **Logboeken** onder **Algemeen**.
1. Voer de volgende query uit waarop u een waarschuwing wilt maken en selecteer vervolgens **uitvoeren**.

    ```kusto
    metrics_resourcemanager_queue_root_default_CL | where AppsFailed_d > 0
    ```

    De query bevat een lijst met mislukte toepassingen die worden uitgevoerd op HDInsight-clusters.

1. Selecteer **nieuwe waarschuwings regel** boven aan de pagina.

    ![Voer een query in om een alert1 te maken](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert-query.png "Voer een query in om een alert1 te maken")

1. Voer in het venster **regel maken** de query en andere details in om een waarschuwing te maken en selecteer vervolgens **waarschuwings regel maken**.

    ![Voer een query in om een alert2 te maken](./media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-create-alert.png "Voer een query in om een alert2 te maken")

### <a name="edit-or-delete-an-existing-alert"></a>Een bestaande waarschuwing bewerken of verwijderen

1. Open de werk ruimte Log Analytics vanuit de Azure Portal.

1. Selecteer in het menu links onder **bewaking** de optie **waarschuwingen**.

1. Selecteer in de bovenste **regel waarschuwings regels beheren**.

1. Selecteer de waarschuwing die u wilt bewerken of verwijderen.

1. U hebt de volgende opties: **Opslaan**, **verwijderen**, **uitschakelen** en **verwijderen**.

    ![Waarschuwing voor verwijderen van HDInsight-Azure Monitor logboeken](media/hdinsight-hadoop-oms-log-analytics-use-queries/hdinsight-log-analytics-edit-alert.png)

Zie [metrische waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../azure-monitor/platform/alerts-metric.md)voor meer informatie.

## <a name="see-also"></a>Zie ook

* [Aan de slag met logboekquery’s in Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
* [Aangepaste weer gaven maken met behulp van de weer gave designer in Azure Monitor](../azure-monitor/platform/view-designer.md)
