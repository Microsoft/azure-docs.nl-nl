---
title: Azure Monitor Logboeken gebruiken voor het bewaken van Azure HDInsight-clusters
description: Meer informatie over het gebruik van Azure Monitor-logboeken voor het bewaken van taken die worden uitgevoerd in een HDInsight-cluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, devx-track-azurepowershell
ms.date: 05/13/2020
ms.openlocfilehash: b81a2021cabefd9587d448aa236e7e87bc254741
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100590816"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor-logboeken gebruiken om HDInsight-clusters te bewaken

Meer informatie over het inschakelen van Azure Monitor logboeken voor het bewaken van Hadoop-cluster bewerkingen in HDInsight. En het toevoegen van een HDInsight-bewakings oplossing.

[Azure monitor-logboeken](../azure-monitor/logs/log-query-overview.md) is een Azure Monitor service die uw Cloud-en on-premises omgevingen bewaakt. De bewaking is om hun Beschik baarheid en prestaties te behouden. Hiermee worden gegevens verzameld die zijn gegenereerd door resources in uw Cloud, on-premises omgevingen en andere controle Programma's. De gegevens worden gebruikt om analyses uit te voeren in meerdere bronnen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Log Analytics-werkruimte. U kunt deze werk ruimte zien als een unieke Azure Monitor-logboek omgeving met een eigen gegevensopslag plaats, gegevens bronnen en oplossingen. Zie [een log Analytics-werk ruimte maken](../azure-monitor/vm/quick-collect-azurevm.md#create-a-workspace)voor instructies.

* Een Azure HDInsight-cluster. Op dit moment kunt u Azure Monitor-Logboeken gebruiken met de volgende HDInsight-cluster typen:

  * Hadoop
  * HBase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Zie [aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)voor instructies over het maken van een HDInsight-cluster.  

* Als u Power shell gebruikt, hebt u de [AZ-module](/powershell/azure/)nodig. Zorg ervoor dat u de meest recente versie hebt. Voer indien nodig uit `Update-Module -Name Az` .

* Als u Azure CLI wilt gebruiken en u deze nog niet hebt geïnstalleerd, raadpleegt u [de Azure cli installeren](/cli/azure/install-azure-cli).

> [!NOTE]  
> Het is raadzaam om zowel het HDInsight-cluster als de Log Analytics-werk ruimte in dezelfde regio te plaatsen voor betere prestaties. Azure Monitor-logboeken zijn niet beschikbaar in alle Azure-regio's.

## <a name="enable-azure-monitor-using-the-portal"></a>Azure Monitor inschakelen met behulp van de portal

In deze sectie configureert u een bestaand HDInsight Hadoop-cluster voor het bewaken van taken, logboeken voor fout opsporing enzovoort, het gebruik van een Azure Log Analytics-werk ruimte.

1. Selecteer uw cluster in de [Azure Portal](https://portal.azure.com/). Het cluster wordt geopend op een nieuwe portal-pagina.

1. Selecteer aan de linkerkant, onder **bewaking**, de optie **Azure monitor**.

1. Selecteer in de hoofd weergave onder **Azure monitor-integratie** de optie **inschakelen**.

1. Selecteer een bestaande Log Analytics-werk ruimte in de vervolg keuzelijst **Selecteer een werk ruimte** .

1. Selecteer **Opslaan**.  Het duurt enkele minuten om de instelling op te slaan.

    ![Bewaking voor HDInsight-clusters inschakelen](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Bewaking voor HDInsight-clusters inschakelen")

## <a name="enable-azure-monitor-using-azure-powershell"></a>Azure Monitor met behulp van Azure PowerShell inschakelen

U kunt Azure Monitor logboeken inschakelen met behulp van de Azure PowerShell AZ module [Enable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) cmdlet.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

Als u wilt uitschakelen, gebruikt u de cmdlet [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) :

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Azure Monitor inschakelen met behulp van Azure CLI

U kunt Azure Monitor logboeken inschakelen met behulp van de Azure CLI `[az hdinsight monitor enable` ]-opdracht (/CLI/Azure/hdinsight/monitor # AZ-hdinsight-monitor-Enable).

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

Als u wilt uitschakelen, gebruikt u de [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az-hdinsight-monitor-disable) opdracht.

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Beheer oplossingen voor HDInsight-clusters installeren

HDInsight biedt platformspecifieke beheer oplossingen die u kunt toevoegen voor Azure Monitor-Logboeken. [Beheer oplossingen](../azure-monitor/insights/solutions.md) voegen functionaliteit toe aan Azure monitor logboeken, waardoor er aanvullende gegevens-en analyse Programma's worden geleverd. Deze oplossingen verzamelen belang rijke metrische gegevens over prestaties van uw HDInsight-clusters. En bieden de hulp middelen om de metrische gegevens te zoeken. Deze oplossingen bieden ook visualisaties en dash boards voor de meeste cluster typen die in HDInsight worden ondersteund. Door de metrische gegevens te gebruiken die u bij de oplossing verzamelt, kunt u aangepaste bewakings regels en waarschuwingen maken.

Beschik bare HDInsight-oplossingen:

* HDInsight Hadoop-bewaking
* HDInsight HBase-bewaking
* Interactieve query bewaking van HDInsight
* HDInsight Kafka-bewaking
* HDInsight Spark-bewaking
* HDInsight Storm-bewaking

Zie [beheer oplossingen in azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution)voor instructies voor beheer oplossingen. Als u wilt experimenteren, installeert u een HDInsight Hadoop-bewakings oplossing. Wanneer u klaar bent, ziet u een **HDInsightHadoop** -tegel die wordt weer gegeven onder **samen vatting**. Selecteer de tegel **HDInsightHadoop** . De HDInsightHadoop-oplossing ziet er als volgt uit:

![Weer gave HDInsight-bewakings oplossing](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Omdat het cluster een gloed nieuw cluster is, worden in het rapport geen activiteiten weer gegeven.

## <a name="configuring-performance-counters"></a>Prestatie meter items configureren

Azure monitor biedt ondersteuning voor het verzamelen en analyseren van prestatie gegevens voor de knoop punten in uw cluster. Zie [Linux-prestatie gegevens bronnen in azure monitor](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters)voor meer informatie.

## <a name="cluster-auditing"></a>Cluster controle

HDInsight ondersteunt cluster controle met Azure Monitor-logboeken door de volgende typen logboeken te importeren:

* `log_gateway_audit_CL` -deze tabel bevat audit logboeken van cluster gateway knooppunten die geslaagde en mislukte aanmeldings pogingen tonen.
* `log_auth_CL` -deze tabel bevat SSH-logboeken met geslaagde en mislukte aanmeldings pogingen.
* `log_ambari_audit_CL` -deze tabel bevat audit logboeken van Ambari.
* `log_ranger_audti_CL` -deze tabel bevat audit logboeken van Apache zwerver op ESP-clusters.

## <a name="next-steps"></a>Volgende stappen

* [Azure Monitor logboeken voor het controleren van HDInsight-clusters doorzoeken](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [De beschik baarheid van clusters bewaken met Apache Ambari en Azure Monitor logs](./hdinsight-cluster-availability.md)