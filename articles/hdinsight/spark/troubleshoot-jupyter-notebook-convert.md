---
title: Kan geen Jupyter Notebook maken in azure HDInsight
description: Meer informatie over de stappen voor probleem oplossing en mogelijke oplossingen voor problemen bij interactie met Azure HDInsight-clusters.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: d5f6612e22522452efb5eecd14c6c825bf29e6d5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938681"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Kan geen Jupyter Notebook maken in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Wanneer u een Jupyter Notebook start, wordt er een fout bericht met de volgende strekking weer gegeven:

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>Oorzaak

Een versie komt niet overeen.

## <a name="resolution"></a>Oplossing

1. Gebruik de [ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster. Voer vervolgens deze opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Open `_version.py` door de volgende opdracht uit te voeren:

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. Wijzig **5** in **4** zodat de gewijzigde lijn er als volgt uitziet:

    ```python
    version_info = (4, 0, 3)
    ```

    Sla de wijzigingen op door **CTRL + X**, **Y**, **Enter** in te voeren.

1. Navigeer in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER`, waarbij `CLUSTERNAME` de naam van uw cluster is.

1. Start de Jupyter-service opnieuw.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
