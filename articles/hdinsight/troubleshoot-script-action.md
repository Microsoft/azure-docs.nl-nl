---
title: Problemen met script acties in azure HDInsight oplossen
description: Algemene stappen voor het oplossen van problemen met script acties in azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 73b958964db2d0b308dd6dfc34024d61ce5ad8af
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871432"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>Problemen met script acties in azure HDInsight oplossen

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="viewing-logs"></a>Logboeken weergeven

U kunt de Web-UI van Apache Ambari gebruiken om informatie te bekijken die is vastgelegd door script acties. Als het script mislukt tijdens het maken van het cluster, bevinden de logboeken zich in het standaard cluster-opslag account. Deze sectie bevat informatie over het ophalen van de logboeken met behulp van beide opties.

### <a name="apache-ambari-web-ui"></a>Apache Ambari-webgebruikersinterface

1. Navigeer in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net`, waarbij `CLUSTERNAME` de naam van uw cluster is.

1. Selecteer de **OPS** -vermelding in de balk boven aan de pagina. Er wordt een lijst weer gegeven met de huidige en vorige bewerkingen die op het cluster zijn uitgevoerd via Ambari.

    :::image type="content" source="./media/troubleshoot-script-action/hdi-apache-ambari-nav.png" alt-text="Ambari Web UI-balk met OPS geselecteerd" border="true":::

1. Zoek de vermeldingen met **\_ customscriptaction** uit in de kolom **bewerkingen** . Deze vermeldingen worden gemaakt wanneer de script acties worden uitgevoerd.

    :::image type="content" source="./media/troubleshoot-script-action/ambari-script-action.png" alt-text="Acties voor Apache Ambari-script actie" border="true":::

    Als u de **stdout** -en **stderr** -uitvoer wilt weer geven, selecteert u de vermelding **run\customscriptaction** en zoomt u in op de koppelingen. Deze uitvoer wordt gegenereerd wanneer het script wordt uitgevoerd en kan nuttige informatie bevatten.

### <a name="default-storage-account"></a>Standaard opslag account

Als het maken van een cluster mislukt vanwege een script fout, worden de logboeken bewaard in het cluster-opslag account.

* De opslag logboeken zijn beschikbaar op `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE` .

    :::image type="content" source="./media/troubleshoot-script-action/script-action-logs-in-storage.png" alt-text="Script actie logboeken" border="true":::

    In deze map worden de logboeken afzonderlijk geordend voor **hoofd knooppunt**, **worker node** en **Zookeeper node**. Zie de volgende voorbeelden:

    * **Hoofd knooppunt**: `<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Worker-knoop punt**: `<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Zookeeper-knoop punt**: `<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Alle **stdout** en **stderr** van de bijbehorende host worden geüpload naar het opslag account. Er zijn één **uitvoer- \* . txt** en **fouten- \* . txt** voor elke script actie. Het **output-*. txt** -bestand bevat informatie over de URI van het script dat op de host is uitgevoerd. De volgende tekst is een voor beeld van deze informatie:

    ```output
    'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'
    ```

* Het is mogelijk dat u herhaaldelijk een script actie cluster met dezelfde naam maakt. In dat geval kunt u de relevante logboeken onderscheiden op basis van de naam van de map **date** . De mapstructuur voor een cluster, **mycluster**, die op verschillende datums wordt gemaakt, lijkt bijvoorbeeld op de volgende logboek vermeldingen:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Als u een script actie cluster met dezelfde naam op dezelfde dag maakt, kunt u het unieke voor voegsel gebruiken om de relevante logboek bestanden te identificeren.

* Als u een cluster maakt in de buurt van 12:00 uur middernacht, is het mogelijk dat de logboek bestanden twee dagen lang zijn. In dat geval ziet u twee verschillende datum mappen voor hetzelfde cluster.

* Het uploaden van logboek bestanden naar de standaard container kan tot vijf minuten duren, met name voor grote clusters. Als u de logboeken wilt openen, moet u het cluster dus niet onmiddellijk verwijderen als een script actie mislukt.

## <a name="ambari-watchdog"></a>Ambari watchdog

Wijzig niet het wacht woord voor de Ambari watchdog, hdinsightwatchdog, op uw op Linux gebaseerd HDInsight-cluster. Een wachtwoord wijziging verbreekt de mogelijkheid om nieuwe script acties uit te voeren op het HDInsight-cluster.

## <a name="cant-import-name-blobservice"></a>Kan de naam BlobService niet importeren

__Symptomen__. De script actie mislukt. Tekst die vergelijkbaar is met de volgende fout wordt weer gegeven wanneer u de bewerking in Ambari bekijkt:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Oorzaak__. Deze fout treedt op als u de python Azure Storage-client bijwerkt die is opgenomen in het HDInsight-cluster. HDInsight verwacht Azure Storage client 0.20.0.

__Oplossing__. Om deze fout op te lossen, moet u hand matig verbinding maken met elk cluster knooppunt met behulp van `ssh` . Voer de volgende opdracht uit om de juiste Storage-client versie opnieuw te installeren:

```bash
sudo pip install azure-storage==0.20.0
```

Zie [verbinding maken met HDInsight (Apache Hadoop) met behulp van SSH](hdinsight-hadoop-linux-use-ssh-unix.md)voor informatie over het maken van verbinding met het cluster met SSH.

## <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>In de geschiedenis worden geen scripts weer gegeven die worden gebruikt tijdens het maken van het cluster

Als uw cluster vóór 15 maart 2016 is gemaakt, ziet u mogelijk geen vermelding in de script actie geschiedenis. Het wijzigen van het formaat van het cluster leidt ertoe dat de scripts worden weer gegeven in de geschiedenis van de script actie.

Er zijn twee uitzonde ringen:

* Uw cluster is gemaakt vóór 1 september 2015. Deze datum is wanneer script acties zijn geïntroduceerd. Alle clusters die vóór deze datum zijn gemaakt, kunnen geen script acties hebben voor het maken van het cluster.

* U hebt meerdere script acties gebruikt tijdens het maken van het cluster. Of u hebt dezelfde naam gebruikt voor meerdere scripts of dezelfde naam, dezelfde URI, maar verschillende para meters voor meerdere scripts. In deze gevallen wordt de volgende fout weer geven:

    ```
    No new script actions can be run on this cluster because of conflicting script names in existing scripts. Script names provided at cluster creation must be all unique. Existing scripts are run on resize.
    ```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]